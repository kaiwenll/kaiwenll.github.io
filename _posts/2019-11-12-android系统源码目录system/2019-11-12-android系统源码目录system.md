```
am.jar：终端下执行am命令时所需的java库。源码目录：framework/base/cmds/am
android.policy.jar：锁屏界面需要用到的jar包，该包引用了android.test.runner.jar，源码目录：framework/base/policy
android.test.runner.jar：测试应用所需的jar包，该包引用了core.jar,core-junit.ajr以及framework.jar，源码目录：framework/base/test-runner
bmgr.jar：adb shell命令下对Android Device所有package备份和恢复的操作时所需的java库。官方文档：http://developer.android.com/guide/developing/tools/bmgr.html。不过这个android服务默认是Disabled，而且要backup的应用必须实现BackupAgent，在AndroidManifest.xml的application标签中加入android：backupAgent属性。源码目录：framework/base/cmds/bmgr
bouncycastle.jar： java三方的密匙库，网上资料说用来apk签名、https链接之类，官网 ：http://www.bouncycastle.org/java.html
com.android.future.usb.accessory.jar：用于管理USB的上层java库，在系统编译时hardware层会调用到。源码目录：frameworks/base/libs/usb
com.android.location.provider.jar：
com.android.nfc_extras.jar：NFC外部库。android/nfc/NfcAdapter.java会调用到包中的NfcAdapterExtras.java。源码目录：frameworks/base/nfc-extras
core-junit.jar ：junit核心库，在运行*Test.apk时被调用。
core-junitrunner.jar：未知，公司话机上有。
core-tests*.jar：framework下的一系列测试jar包，不做测试时可删除。
core.jar：核心库，启动桌面时首先加载这个。源码目录： 
ext.jar：android外部三方扩展包，源码主要是external/nist-sip（java下的sip三方库）、external/apache-http（apache的java三方库）、external/tagsoup（符合SAX标准的HTML解析器）。其实这个jar包可以添加外部扩展jar包，只需在framework/base/Android.mk中的ext-dir添加src目录即可。
framework-res.apk：android系统资源库。
framework.jar：android的sdk中核心代码。
ime.jar：ime命令所需jar包，用于查看当前话机输入法列表、设置输入法。源码目录：framework/base/cmds/ime
input.jar：input命令所需的jar包，用于模拟按键输入。源码目录：framework/baes/cmds/input
javax.obex.jar：java蓝牙API，用于对象交换协议。源码目录：framework/base/obex
monkey.jar：执行monkey命令所需jar包。源码目录：framework/base/cmds/monkey
pm.jar：执行pm命令所需的jar包，pm详情见adb shell pm，源码目录：framework/base/cmds/pm
services.jar：话机框架层服务端的编译后jar包，配合libandroid_servers.so在话机启动时通过SystemServer以循环闭合管理的方式将各个service添加到ServiceManager中。源码目录：framework/base/service
sqlite-jdbc.jar： sqlite的Java DataBase Connextivity jar包。
svc.jar：svc命令所需jar包，可硬用来管理wifi,power和data。源码目录：framework/base/cmds/svc，详情见：http://madgoat.cn/2011/02/android_svc/
 ```
 以上都是自己的理解或者网上查找资料后获得的，如有误点，欢迎指出。
http://blog.sina.com.cn/s/blog_757397730101efy7.html

### 1.目录树
/framework/base/api 
/framework/base/awt
/framework/base/build

/framework/base/camera
关 于camera的HAL接口库。最终生成native共享库libcamera.so,编译时根据是否定义USE_CAMERA_STUB来决定系统是否有Camera硬件支持。若没有实际的Camera硬件，则编译时会和虚拟camera静态库（libcamerastub.a,由camerahardwarestub.cpp,fakecamera生成）链接生成libcamera.so。

/framework/base/cmds关于android系统启动时用到的command等

/framework/base/cmds/am

/framework/base/cmds/app_process 
可执行文件app_process，该文件可以根据输入参数决定是Zygote启动（参考init.rc中的语句 service zygote/system/bin/app_process -Xzygote /system/bin --zygote--start-system-server）.
该执行程式会链接libandroid_runtime.so去链接androidruntime。后面我会在详细分析此部分。

/framework/base/cmds/backup 
可执行程式btool

/framework/base/cmds/bmgr
java可执行程式， backup manager,java库形式分发到目标系统/system/framework/bmgr.jar

/framework/base/cmds/bootanimation
android启动动画效果程式，该程式必须在androidruntime启动后运行。

/framework/base/cmds/dumpstate
android系统调试辅助工具,生成可执行程式dumpstate,同时建立两个程式dumpcrashbugreport指向该程式。

/framework/base/cmds/dumpsys
生成可执行程式dumpsys

/framework/base/cmds/ime
java可执行程式 ，IME输入法 input method manager,java库形式分发到目标系统/system/framework/ime.jar

/framework/base/cmds/input
java可执行程式，管理input事件例如key event,text event等，java库形式分发到目标系统/system/framework/input.jar

/framework/base/cmds/installd
可执行程式installd,installmanager,仅在非simulator系统中运行，安装到目标系统/system/bin/installd

/framework/base/cmds/keystore
可执行程式keystore,用途？？？仅在非simulator系统中运行，安装到目标系统/system/bin/keystore

/framework/base/cmds/pm
java可执行程式，packagemanager，java库形式分发到目标系统/system/framework/pm.jar

/framework/base/cmds/runtime
runtime可执行程式，仅在simulator中使用

/framework/base/cmds/service
service可执行程式，用来查找，检查，呼叫service，安装到目标系统/system/bin/service

/framework/base/cmds/servicemanager
android系统的servicemanager，可执行文件，安装到目标系统/system/bin/servicemanager
servicemanager会和kernel的binderdriver协作共同完成service的添加、查询、获取、检查等。

/framework/base/cmds/surfaceflinger
surfaceflinger可执行程式，安装到目标系统/system/bin/surfaceflinger,
该程式会初始化surfaceflinger,surfaceflinger::inistantiate(),该程式会链接到libsurfaceflinger.so

/framework/base/cmds/svc

/framework/base/cmds/system_server
systemserver库libsystem_server.so->system/lib/libsystem_server.so和system_server可执行程式->system/bin/system_server.

/framework/core/
/framework/core/config
几个简单java常量，（debug标志等）

/framework/core/java/*
framework的核心，此处主要指applicationframework,java库形式分发到/system/framework/
包括framework.jar,framework-tests.jar

/framework/core/jni
framework所需的JNI接口实现库,分发到/system/lib/lib/libandroid_runtime.so

/framework/core/res
framework所需的资源文件打包，/system/framework/framework-res.apk,

/framework/include
存放头文件*.h

/framework/libs
/framework/libs/audioflinger,
生成libaudioflinger.so,
若无实际硬件和静态库libaudiointerface.a（audiointerface虚拟设备）链接。
若有实际硬件和libaudio.so链接，若支持bluetooth，则和liba2dp.so链接

/framework/libs/surfaceflinger
生成libsurfaceflinger.so

/framework/libs/ui
生成libui.so

/framework/libs/utils
生成libutils.so

/framework/services/java/*
system serverjava可执行程式service.jar,分发到/system/framework/service.jar

/framework/services/jni/*
system serverJNI接口实现库,libanroid_servers.so，分发到/system/lib/libanroid_servers.sosystemserverJNI接口实现库,libanroid_servers.so，分发到/system/lib/libanroid_servers.so


### 启动 Zygote
-Xzygote /system/bin --zygote--start-system-server
AndroidRuntime->AppRuntime
int main(int argc,const char* constargv[])
{
  AppRuntimeruntime;生成AndroidRuntime实例
  ...
 AndroidRuntime.Start("com.android.internal.os.ZygoteInit",startSystemServer);
}
其中AndroidRuntime.Start("com.android.internal.os.ZygoteInit",startSystemServer);
呼叫Android::Start(const char* className,const boolstartSystemServer)
/framework/base/core/jni/AndroidRuntime.cpp
该函数的处理内容：
1.处理Jave Virtual Machine的一些参数选项；
2.创建DalvikJava虚拟机，JNI_CreateJavaVM(&mJavaVM,&env,&initArgs)；
3.注册Android Runtime中的JNI接口给虚拟机；
4.呼叫Java类com.android.internal.os.ZygoteInit的main函数

### 在类com.android.internal.os.ZygoteInit的main函数中，
1.注册Zygote socket用来接收请求;
2.加载preloadedclass、resources用来加快启动速度，文件清单在framework.jar中的preloaded-classes,framework-res.apk中的res中;
3.启动System Server;
 fork出独立的进程名称为system-server，呼叫com.android.server.SystemServer类的main函数；
 在HandleSystemServerProcess函数中，RuntimeInit.ZygoteInit调用会呼叫AppRuntime的

OnZygoteInit函数
4.RuntimeInit.ZygoteInit函数会呼叫com.android.server.SystemServer类的main函数。
 在此main函数中，系统首先加载android_server共享库libandroid_server.so源代码位于/framework/base/service/jni
 在该库中有定义JNI_OnLoad函数，所以Dalvik在加载libandroid_server.so的时候会首先呼叫该JNI_OnLoad函数，该函数将android server注册到Java虚拟机中，包括KeyInputQueue,HardwareService,AlarmManager,BatteryService,SensorService,SystemServer等；
 呼叫在libanroid_server.so中注册的native函数init1，该函数位于/frameworks/base/services/jni/com_android_server_SystemServer.cpp中；
 init1函数呼叫libsystem_server中的system_init函数，该函数位于/frameworks/base/cmds/system_server/library/system_init.cpp中，该函数将SurfaceFlinger/AudioFlinger/MediaPlayer/CameraService等组件注册到ServiceManager中
 system_init函数反过来呼叫java类com.android.server.SystemServer的init2函数；
在init2函数中，android创建了serverthread，在该thread中android开始注册各种service到servicemanager中
包括EntropyService,PowerManager,ActivityManager,Telephony,PackageManager,ContentManager,ContentProvider,
BatteryService,HardwareService,AlarmManager等等。
 注意该线程使用Loope
