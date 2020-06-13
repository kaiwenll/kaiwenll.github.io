## 系统数据库Settings属性使用及相关介绍
### 1.用途及作用:

 alps\frameworks\base\core\java\android\provider\Settings.java
Settings下的属性实际分为System，Global等，一般作用是用于存储系统默认属性值，通过监听读写属性值的变化执行相关的逻辑修改系统属性值，也可以用于系统间跨进程通信。

### 2.基本使用:

2.1读字符串
```
Settings.System.getString(ContentResolver resolver, String name);
Settings.System.getString(mContext.getContentResolver(), String name);
```
2.2写字符串
```
Settings.System.putString(ContentResolver resolver, String name, String value);
8Settings.System.putString(mContext.getContentResolver(),String value);
```

### 3.系统调用流程

以默认亮度为例，最开始定义是在
alps\frameworks\base\packages\SettingsProvider\res\values\defaults.xml里面的
<integer name="def_screen_brightness">102</integer>，之后会在
\alps\frameworks\base\packages\SettingsProvider\src\com\android\providers\settings\DatabaseHelper.java中的loadSystemSettings(SQLiteDatabase db)中
loadIntegerSetting(stmt, Settings.System.SCREEN_BRIGHTNESS, R.integer.def_screen_brightness);被加载进来，然后会存入系统的数据库中
alps\frameworks\base\core\java\android\provider\Settings.java
```java
         /**
         * The screen backlight brightness between 0 and 255.
         */

        public static final String SCREEN_BRIGHTNESS = "screen_brightness";
```
数据保存在机器的```data/data/com.android.providers.settings/databases/Settings.db```文件里可以通过导出表格查看数据.
当亮度的值发生改变之后会调用Settings.System的put方法存入数据库中。
说明:参考以上流程可以添加 自定义的数据库逻辑，可用于系统进程间数据通信，类似于apk中的偏好和数据库，优点是各个apk及服务间可以直接调用

### 4.数据变化监听

实际以adb开关为例，当设置中打开关闭adb时会往Settings.Global.ADB_ENABLED中写值，当值变化的时候会走onchange中去执行实际的adb开关的功能逻辑
```java
    private class AdbSettingsObserver extends ContentObserver {
        public AdbSettingsObserver() {
            super(null);
        }
        @Override
        public void onChange(boolean selfChange) {
            boolean enable = (Settings.Global.getInt(mContentResolver,
                    Settings.Global.ADB_ENABLED, 0) > 0);
            mHandler.sendMessage(MSG_ENABLE_ADB, enable);
        }

    }

import android.database.ContentObserver;
mContext.getContentResolver().registerContentObserver(Settings.System.getUriFor(Settings.System.SCREENSHOT_BUTTON_SHOW), false, mScreenshotButtonShowObserver);

private ContentObserver mScreenshotButtonShowObserver=new ContentObserver(new Handler()) {
        public void onChange(boolean selfChange) {
            boolean isShow=Settings.System.getInt(mContext.getContentResolver(), 
Settings.System.SCREENSHOT_BUTTON_SHOW, 1)==1;
            
        };
    };

```
### 5.总结

>  出发点是授人以渔，而不是授人以鱼，很多修改系统默认属性的值都是直接改default.xml的具体数值就可以了，常用的也是百度能百度到，当碰到不熟悉的默认属性值的时候，比如是设置里面的adb开关，可以去设置里面去跟代码找到那个点击事件的逻辑，那里应该会有个开关动作的具体逻辑，跟下去会发现是会去写值操作，然后找到那个ADB_ENABLED字符，然后去framework/base下面去搜索便可以跟踪相关的逻辑，一般的流程系统在设置默认值的时候都是在一个地方定义一个默认值，然后将这个值存入数据库中，修改的时候就将修改的值重新写进入，然后通过数据监听去实现具体的逻辑。与此同时做客制化的修改的时候可能会需要在不同的apk或者进程间通信，此时可以利用系统的数据库的读写操作实现进程间通信和数据共享的功能。
***
版权声明：本文为CSDN博主「xiaozheng532345722」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/u010672559/article/details/79932008
