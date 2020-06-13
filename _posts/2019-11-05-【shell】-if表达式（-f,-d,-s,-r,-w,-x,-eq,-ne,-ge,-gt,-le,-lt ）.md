### 文件表达式
```
if [ -f file ] 如果文件存在
if [ -d … ] 如果目录存在
if [ -s file ] 如果文件存在且非空
if [ -r file ] 如果文件存在且可读
if [ -w file ] 如果文件存在且可写
if [ -x file ] 如果文件存在且可执行
```
### 整数变量表达式
```
if [ int1 -eq int2 ] 如果int1等于int2
if [ int1 -ne int2 ] 如果不等于
if [ int1 -ge int2 ] 如果>=
if [ int1 -gt int2 ] 如果>
if [ int1 -le int2 ] 如果<=
if [ int1 -lt int2 ] 如果<
```

### 字符串变量表达式
```
If [ $a = $b ] 如果string1等于string2
字符串允许使用赋值号做等号
if [ $string1 != $string2 ] 如果string1不等于string2
if [ -n $string ] 如果string 非空(非0），返回0(true)
if [ -z $string ] 如果string 为空
if [ $sting ] 如果string 非空，返回0 (和-n类似)
```

### s​h​e​l​l​中​条​件​判​断​i​f​中​的​-​z​到​-​d​的​意​思
```
[ -a FILE ] 如果 FILE 存在则为真。
[ -b FILE ] 如果 FILE 存在且是一个块特殊文件则为真。

[ -c FILE ] 如果 FILE 存在且是一个字特殊文件则为真。

[ -d FILE ] 如果 FILE 存在且是一个目录则为真。

[ -e FILE ] 如果 FILE 存在则为真。
[ -f FILE ] 如果 FILE 存在且是一个普通文件则为真。

[ -g FILE ] 如果 FILE 存在且已经设置了SGID则为真。
[ -h FILE ] 如果 FILE 存在且是一个符号连接则为真。

[ -k FILE ] 如果 FILE 存在且已经设置了粘制位则为真。 [

-p FILE ] 如果 FILE 存在且是一个名字管道(F如果O)则为真。

[ -r FILE ] 如果 FILE 存在且是可读的则为真。

[ -s FILE ] 如果 FILE 存在且大小不为0则为真。
[ -t FD ] 如果文件描述符 FD 打开且指向一个终端则为真。

[ -u FILE ] 如果 FILE 存在且设置了SUID (set user ID)则为真。

[ -w FILE ] 如果 FILE 如果 FILE 存在且是可写的则为真。

[ -x FILE ] 如果 FILE 存在且是可执行的则为真。

[ -O FILE ] 如果 FILE 存在且属有效用户ID则为真。

[ -G FILE ] 如果 FILE 存在且属有效用户组则为真。 [ -L FILE ] 如果 FILE 存在且是一个符号连接则为真。
[ -N FILE ] 如果 FILE 存在 and has been mod如果ied since it was last read则为真。
[ -S FILE ] 如果 FILE 存在且是一个套接字则为真。
[ FILE1 -nt FILE2 ] 如果 FILE1 has been changed more recently than FILE2,or 如果 FILE1 exists and FILE2 does not则为真。
[ FILE1 -ot FILE2 ] 如果 FILE1 比 FILE2 要老, 或者 FILE2 存在且 FILE1 不存在则为真。
[ FILE1 -ef FILE2 ] 如果 FILE1 和 FILE2 指向相同的设备和节点号则为真。

[ -o OPTIONNAME ] 如果 shell选项 “OPTIONNAME” 开启则为真。

[ -z STRING ] “STRING” 的长度为零则为真。
```
————————————————
版权声明：本文为CSDN博主「打卤」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/liyyzz33/article/details/84836255
