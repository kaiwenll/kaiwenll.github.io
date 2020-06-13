# 常用命令

### vim命令

```
TlistToggle //显示函数列表
/ \c //查找不区分大小写
跳转到上/下一个修改的位置 
当你编辑一个很大的文件时，经常要做的事是在某处进行修改，然后跳到另外一处。如果你想跳回之前修改的地方，使用命令：
Ctrl+o
来回到之前修改的地方
类似的：
Ctrl+i
会回退上面的跳动。
[{
]}
  替换所有行的内容：      :%s/from/to/g
        :%s/from/to/g   ：  对所有行的内容进行替换。
  在VIM中进行文本替换：
```
### vim替换文本：
>    
    1.  替换当前行中的内容：    :s/from/to/    （s即substitude）
        :s/from/to/     ：  将当前行中的第一个from，替换成to。如果当前行含有多个
                            from，则只会替换其中的第一个。
        :s/from/to/g    ：  将当前行中的所有from都替换成to。
        :s/from/to/gc   ：  将当前行中的所有from都替换成to，但是每一次替换之前都
                            会询问请求用户确认此操作。
        注意：这里的from和to都可以是任何字符串，其中from还可以是正则表达式。
>
    2.  替换某一行的内容：      :33s/from/to/g
        :.s/from/to/g   ：  在当前行进行替换操作。
        :33s/from/to/g  ：  在第33行进行替换操作。
        :$s/from/to/g   ：  在最后一行进行替换操作。

> 
    3.  替换某些行的内容：      :10,20s/from/to/g
        :10,20s/from/to/g   ：  对第10行到第20行的内容进行替换。
        :1,$s/from/to/g     ：  对第一行到最后一行的内容进行替换（即全部文本）。
        :1,.s/from/to/g     ：  对第一行到当前行的内容进行替换。
        :.,$s/from/to/g     ：  对当前行到最后一行的内容进行替换。
        :'a,'bs/from/to/g   ：  对标记a和b之间的行（含a和b所在的行）进行替换。
                                其中a和b是之前用m命令所做的标记。
>    
    4.  替换所有行的内容：      :%s/from/to/g
        :%s/from/to/g   ：  对所有行的内容进行替换。
>    
    5.  替换命令的完整形式：    :[range]s/from/to/[flags]
        5.1 s/from/to/
            把from指定的字符串替换成to指定的字符串，from可以是正则表达式。
        5.2 [range]
            有以下一些表示方法：
            不写range   ：  默认为光标所在的行。
            .           ：  光标所在的行。
            1           ：  第一行。
            $           ：  最后一行。
            33          ：  第33行。
            'a          ：  标记a所在的行（之前要使用ma做过标记）。
            .+1         ：  当前光标所在行的下面一行。
            $-1         ：  倒数第二行。（这里说明我们可以对某一行加减某个数值来
                            取得相对的行）。
            22,33       ：  第22～33行。
            1,$         ：  第1行 到 最后一行。
            1,.         ：  第1行 到 当前行。
            .,$         ：  当前行 到 最后一行。
            'a,'b       ：  标记a所在的行 到标记b所在的行。
            %           ：  所有行（与 1,$ 等价）。
            ?chapter?   ：  从当前位置向上搜索，找到的第一个chapter所在的行。（
                            其中chapter可以是任何字符串或者正则表达式。
            /chapter/   ：  从当前位置向下搜索，找到的第一个chapter所在的行。（
                            其中chapter可以是任何字符串或者正则表达式。
            注意，上面的所有用于range的表示方法都可以通过 +、- 操作来设置相对偏
            移量。
        5.3 [flags]
            这里可用的flags有：
            无      ：  只对指定范围内的第一个匹配项进行替换。
            g       ：  对指定范围内的所有匹配项进行替换。
            c       ：  在替换前请求用户确认。
            e       ：  忽略执行过程中的错误。
            注意：上面的所有flags都可以组合起来使用，比如 gc 表示对指定范围内的
            所有匹配项进行替换，并且在每一次替换之前都会请用户确认。
### 网络 

```
busybox ifconfig eth0 up
```
### amloggic获取遥控器按键信息

首先清理内核中的信息

```
dmesg //获取内核报告的信息
dmesg -c  //清理内核中的信息
```
按顺序按遍遥控器的每个按钮（如果你觉得没按到，你可以多按几次，但一定要按顺序） 

```
dmesg | grep "code is 0x" | rev |cut -c 5-6,9-10| rev |uniq >> /sdcard/IRdump.log
```
然后会在/sdcard/下创建包含scancodes的IRdump.log文件，这些会被用在remote.conf文件中，所以请务必按顺序按下，否则之后你会搞乱。

下一步就是remote.conf文件了，默认路径是

```
/system/etc/remote.conf
```

这里演示通过apk名字查找包名，并最终用am命令启动Activity

```
package:/system/app/SWSettings_tv.apk=net.sunniwell.app.swsettings
net.sunniwell.app.swsettings/.SWSettingsActivity

am start net.sunniwell.app.swsettings/.SWSettingsActivity

package:/system/priv-app/Settings.apk=com.android.settings
package:/system/app/SWSettings_base_KitKat.apk=net.sunniwell.settings
package:/system/priv-app/SettingsProvider.apk=com.android.providers.settings
```
```
//查看本地包名

pm list packages -f | grep -i local

package:/system/app/SWLocalPlayer_tv.apk=net.sunniwell.app.localplayer
dumpsys package net.sunniwell.app.localplayer

net.sunniwell.app.localplayer/.SWLocalPlayerAllActivity
am broadcast -a net.sunniwell.action.LOCALPLAY_ABLUM 显示一个页面

net.sunniwell.app.localplayer/.SWLocalPlayerActivity

//原生设置包名
com.android.settings/.Settings$UserSettingsActivity 
```
amlogic system 分区设置为WR

```
echo 1 > /sys/class/remount/need_remount;mount -o rw,remount /system
```

### logo制作

​    制作bmp格式的开机logo图片，分辨率建议为1080p,重命名为bootup,覆盖到device/amlogic/p201_iptv/res_pack$
如果是单独调试，可以按如下步骤，单独制作：

```
./out/host/linux-x86/bin/imgpack
-r  device/amlogic/p201_iptv/res_pack/ 
out/target/product/p201_iptv/res-package.img
```
__参数说明__

res_pack是源图片路径，
res-package.img是生成文件，即logo.img 

#### logo升级
Amlogic除特殊分区外都可以采用cat命令的形式替换分区文件，例如recovery、log等分区。
Logo的升级操作

```
cat /storage/external_storage/sda1/res-package.img  > /dev/block/logo
```


另外，amlogic支持动态更新开机logo，系统提供API，更新开机logo图片，支持jpg, png, bmp等常见格式，重启生效。 

小系统logo.img 目录 platform/on-project/pub/images
~/project/s905l2/platform/release/images/logo.img 不对的，一般不修改release目录下的内容

#### 开机动画
./platform/release/system/media

### android平台的三个编译命令----make,mm,mmm
在android源码根目录下，执行以下三步即可编译android:
1. build/envsetup.sh #这个脚本用来设置android的编译环境;
2. lunch #选择编译目标
3. make #编译android整个系统

### android平台提供了三个命令用于编译，这3个命令分别为：
```
1. make: 不带任何参数则是编译整个系统；
make MediaProvider： 单个模块编译，会把该模块及其依赖的其他模块一起编译(会搜索整个源代码来定位MediaProvider模块所使用的Android.mk文件，还要判断该模块依赖的其他模块是否有修改)；
2. mmm packages/providers/MediaProvider: 编译指定目录下的模块，但不编译它所依赖的其它模块；
3. mm: 编译当前目录下的模块，它和mmm一样，不编译依赖模块;
4. mma: 编译当前目录下的模块及其依赖项
以上三个命令都可以用-B选项来重新编译所有目标文件。
```

## Android系统命令
### logcat
#### 1、清空日志缓冲区
logcat -c
#### 2、抓取所有日志，在每行日志前增加时间戳
logcat -v time
#### 3、抓取所有日志，在每行日志前增加时间戳和线程信息
logcat -v threadtime
#### 4、过滤抓取日志，按xxx标签抓取
logcat -s xxx
#### 5、输出日志到文件
logcat -f /sdcard/log.txt
#### 6、logcat --help
```
255|root@Hi3798MV300:/ # logcat --help
Usage: logcat [options] [filterspecs]
options include:
  -s              Set default filter to silent.
                  Like specifying filterspec '*:s'
  -f <filename>   Log to file. Default to stdout
  -r [<kbytes>]   Rotate log every kbytes. (16 if unspecified). Requires -f
  -n <count>      Sets max number of rotated logs to <count>, default 4
  -v <format>     Sets the log print format, where <format> is one of:

                  brief process tag thread raw time threadtime long

  -c              clear (flush) the entire log and exit
  -d              dump the log and then exit (don't block)
  -t <count>      print only the most recent <count> lines (implies -d)
  -g              get the size of the log's ring buffer and exit
  -b <buffer>     Request alternate ring buffer, 'main', 'system', 'radio'
                  or 'events'. Multiple -b parameters are allowed and the
                  results are interleaved. The default is -b main -b system.
  -B              output the log in binary
filterspecs are a series of 
  <tag>[:priority]

where <tag> is a log component tag (or * for all) and priority is:
  V    Verbose
  D    Debug
  I    Info
  W    Warn
  E    Error
  F    Fatal
  S    Silent (supress all output)

'*' means '*:d' and <tag> by itself means <tag>:v

If not specified on the commandline, filterspec is set from ANDROID_LOG_TAGS.
If no filterspec is found, filter defaults to '*:I'

If not specified with -v, format is set from ANDROID_PRINTF_LOG
or defaults to "brief"
```
#### 7、多关键词抓取日志
logcat | grep -E "1111|dddd"
### dd命令
DD是Linux/UNIX 下的一个非常有用的命令，作用是用指定大小的块拷贝一个文件，并在拷贝的同时进行指定的转换。

#### 1、将终端的boot分区备份到/data下
dd if=/dev/block//platform/soc/by-name/boot of=/data/boot.img
#### 2、将/data/下的boot.img，写入到终端boot分区
dd if=/data/boot.img of=/dev/block//platform/soc/by-name/boot
#### 3、支持的所有参数
```
if=文件名：输入文件名，缺省为标准输入。即指定源文件。<if=inputfile>
ibs=bytes：一次读入bytes个字节，即指定一个块大小为bytes个字节。
obs=bytes：一次输出bytes个字节，即指定一个块大小为bytes个字节。
bs=bytes：同时设置读入/输出的块大小为bytes个字节。
cbs=bytes：一次转换bytes个字节，即指定转换缓冲区大小。
skip=blocks：从输入文件开头跳过blocks个块后再开始复制。
seek=blocks：从输出文件开头跳过blocks个块后再开始复制。
注意：通常只用当输出文件是磁盘或磁带时才有效，即备份到磁盘或磁带时才有效。
count=blocks：仅拷贝blocks个块，块大小等于ibs指定的字节数。
conv=conversion：用指定的参数转换文件。
ascii：转换ebcdic为ascii
ebcdic：转换ascii为ebcdic
ibm：转换ascii为alternateebcdic
block：把每一行转换为长度为cbs，不足部分用空格填充
unblock：使每一行的长度都为cbs，不足部分用空格填充
lcase：把大写字符转换为小写字符
ucase：把小写字符转换为大写字符
swab：交换输入的每对字节
noerror：出错时不停止
notrunc：不截短输出文件
sync：将每个输入块填充到ibs个字节，不足部分用空（NUL）字符补齐。
of=文件名：输出文件名，缺省为标准输出。即指定目的文件。< of=output file >
```
