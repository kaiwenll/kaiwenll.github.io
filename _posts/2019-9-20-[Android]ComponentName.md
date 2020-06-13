## **壁纸不显示问题总结**

 + 通过日志分析 ``` W/WallpaperManager(pid):WallpaperService not running```初步定位是服务没有开启的原因

 + 在 ***/system/build.prop*** 中增加 ***sys.wallpaper.enable=true*** 之后继续追查 log 

    + 首先开机之后查看服务状态是正确的
     
      ```
      root@p201_iptv:/ # getprop | grep wallpaper
      [sys.wallpaper.enable]: [true]
      ```
     
    + 发现log中还是会打印``` W/WallpaperManager(pid):WallpaperService not running```

   ***

 + 然后代码追定位到 ***frameworks/base/services/java/com/android/server/SystemServer.java***

   *honestly speaking , I don't know why direct here , but chaoyang bro let me do that.*

   + ### 搜索 Wallpaper 找到系统服务添加wallpaper的代码

     ```
     if (!disableNonCoreServices && context.getResources().getBoolean(
                             R.bool.config_enableWallpaperService) &&
                             SystemProperties.getBoolean("sys.wallpaper.enable", false)) {
                     try {
                         Slog.i(TAG, "Wallpaper Service");
                         if (!headless) {               
                             wallpaper = new WallpaperManagerService(context);
                             ServiceManager.addService(Context.WALLPAPER_SERVICE, wallpaper);
                         }         
                     } catch (Throwable e) {        
                         reportWtf("starting Wallpaper Service", e);
                     }
                 }
     
     ```

     ### 继续查找，***initAndLoop()*** 方法里，创建了一个对象 *wallpaperF*

     ```
     final WallpaperManagerService wallpaperF = wallpaper;
     ```

     ### 这里会检测这个标志位，如果不为1，就创建一个 systemUi 线程

     ```
                     Thread th = new Thread("test") {
                         public void run() {
                             try {
                                 Thread.sleep(3000);
                             } catch (Exception e) {}
                             if (!headless) {
                                 startSystemUi(contextF);
                             }
                         }
                     };
                     if (SystemProperties.getBoolean("sys.wallpaper.enable", false))
                         th.start();
     
     ```

     

     ###  ***wallpaperF.systemRunning（）*** 

     ```
     
                     try {
                         if (wallpaperF != null) wallpaperF.systemRunning();
                     } catch (Throwable e) {        
                         reportWtf("Notifying WallpaperService running", e);
                     }
                     Slog.i(TAG, "BootStage Notifying WallpaperService running");
     
     ```

     

     ### 继续追到 ***WallpaperManagerService.java*** 

      可以看到 ***ComponentName***  这里 调用了包名 ***com.android.systemui*** ，百度一下这个包名对应的 apk 是 ***SystemUI.apk*** ，使用 ***pm install*** 安装这个apk , 背景图片显示正常

     ```
         /**  
          * Name of the component used to display bitmap wallpapers from either the gallery or
          * built-in wallpapers.
          */
         static final ComponentName IMAGE_WALLPAPER = new ComponentName("com.android.systemui",
                 "com.android.systemui.ImageWallpaper");
     ```

     

     ### 这里简单介绍一下 ***ComponentName*** 的使用

     ComponentName：可以启动其他应用的Activity、Service.

     ```
     ComponentName    chatActivity =new ComponentName(param1,param2);
     
     param1:Activity、Service所在应用的包名
     
     param2:Activity、Service的包名+类名
     ```

     Activity:

     ```
      ComponentName chatActivity =new ComponentName("com.npf.chat", "com.npf.chat.ui.ChatActivity");
     
         Intent intent =new Intent();
     
         intent.setComponent(chatActivity);
     
         startActivity(intent);
     ```

     Service:

     ```
       ComponentName chatService =new ComponentName("com.npf.chat", "com.npf.chat.ui.ChatService");
     
         Intent intent =new Intent();
     
         intent.setComponent(chatService );
     
         startService(intent);
     ```

     注:

     ```
     如果该Activity非应用入口(入口Activity默认android:exported="true")，则需要再清单文件中添加   android:exported="true"。Service也需要添加android:exported="true"。允许外部应用调用。
     
        <activity android:name="com.npf.chat.ui.ChatActivity"
     
         android:exported="true"/>
     
        <service android:name="com.npf.chat.ui.ChatService"
     
         android:exported="true"/>
     ```

     
## ComponenetName ： 组件名称
Java中可以通过Intent.setComponent(ComponentName) 来启动其他应用（项目）的Activity 或者Service 。

创建ComponentName对象需要两个参数：

package name (String)：
这里的包名为String格式，一般与Manifest中的package 值相同。
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.test"
    android:versionCode="1"
    android:versionName="1.0" >
```
acitivity name (String)：
第二个参数为目标Activity/Service 全称（包名+类名），String 格式。
```java
    String packageName = "com.example.test";
    String activityName = "com.example.test.MyActivity";
    String serviceName = "com.example.test.MyService";

    //启动Activity
    Intent intentActivity = new Intent();
    ComponentName componentNameActivity = new ComponentName(packageName ,activityName);
    intentActivity.setComponent(componentNameActivity);
    startActivity(intentActivity);

    //启动Service
    Intent intentService = new Intent();
    ComponentName componentNameService = new ComponentName(packageName ,serviceName);
    intentService.setComponent(componentNameService);
    startActivity(intentService);
```
Tips：
如果请求的Activity 是其他应用的入口Activity，即在Manifest中：
```xml
<intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
```
则此时默认 android:exported="true"，无需设置也可以成功调用。若不是，则需要手动添加android:exported="true"。
