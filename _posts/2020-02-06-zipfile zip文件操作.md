#### ZipFile.getinfo(name)

功能：获取zip文档内指定文件的信息。返回一个zipfile.ZipInfo对象，它包括文件的详细信息。将在下面 具体介绍该对象。

#### ZipFile.infolist()

功能：获取zip文档内所有文件的信息，返回一个zipfile.ZipInfo的列表。

#### ZipFile.namelist()

功能：获取zip文档内所有文件的名称列表。

#### ZipFile.extract(member[, path[, pwd]])

功能：将zip文档内的指定文件解压到当前目录。

参数：

    member      指定要解压的文件名称或对应的ZipInfo对象

    path        指定解析文件保存的文件夹

    pwd         解压密码

下面一个例子将保存在程序根目录下的txt.zip内的所有文件解压到D:/Work目录：
```
import zipfile, os

zipFile = zipfile.ZipFile(os.path.join(os.getcwd(), 'txt.zip'))

for file in zipFile.namelist():

    zipFile.extract(file, r'd:/Work')

zipFile.close()



import zipfile, os

zipFile = zipfile.ZipFile(os.path.join(os.getcwd(), 'txt.zip'))

for file in zipFile.namelist():

    zipFile.extract(file, r'd:/Work')

zipFile.close()
```
#### ZipFile.extractall([path[, members[, pwd]]])

功能：解压zip文档中的所有文件到当前目录。

参数：

    members      默认值为zip文档内的所有文件名称列表，也可以自己设置，选择要解压的文件名称。

#### ZipFile.printdir()

功能：将zip文档内的信息打印到控制台上。

#### ZipFile.setpassword(pwd)

功能：设置zip文档的密码。

#### ZipFile.read(name[, pwd])

功能：获取zip文档内指定文件的二进制数据。

下面的例子演示了read()的使用，zip文档内包括一个txt.txt的文本文件，使用read()方法读取其二进制数据，然后保存到D:/txt.txt。
```
    import zipfile, os

    zipFile = zipfile.ZipFile(os.path.join(os.getcwd(), 'txt.zip'))

    data = zipFile.read('txt.txt')

    #一行语句就完成了写文件操作。仔细琢磨哦~_~

    (lambda f, d: (f.write(d), f.close()))(open(r'd:/txt.txt', 'wb'), data)

    zipFile.close()
```
#### ZipFile.write(filename[, arcname[, compress_type]])

功能：将指定文件添加到zip文档中。

参数：

    filename      文件路径

    arcname       添加到zip文档之后保存的名称

    compress_type 压缩方法，它的值可以是zipfile.ZIP_STORED 或zipfile.ZIP_DEFLATED

#### ZipFile.writestr(zinfo_or_arcname, bytes)

功能：writestr()支持将二进制数据直接写入到压缩文档。

#### ZipFile.getinfo(name)

功能：返回一个ZipInfo对象，表示zip文档中相应文件的信息。

它支持如下属性：

    ZipInfo.filename        获取文件名称。

    ZipInfo.date_time       获取文件最后修改时间。返回一个包含6个元素的元组：(年, 月, 日, 时, 分, 秒)

    ZipInfo.compress_type   压缩类型。

    ZipInfo.comment         文档说明。

    ZipInfo.extr            扩展项数据。

    ZipInfo.create_system   获取创建该zip文档的系统。

    ZipInfo.create_version  获取、创建zip文档的PKZIP版本。

    ZipInfo.extract_versio  获取、解压zip文档所需的PKZIP版本。

    ZipInfo.reserved        预留字段，当前实现总是返回0。

    ZipInfo.flag_bits       zip标志位。

    ZipInfo.volume          文件头的卷标。

    ZipInfo.internal_attr   内部属性。

    ZipInfo.external_attr   外部属性。

    ZipInfo.header_offset   文件头偏移位。

    ZipInfo.CRC             未压缩文件的CRC-32。

    ZipInfo.compress_size   获取压缩后的大小。

    ZipInfo.file_size       获取未压缩的文件大小

 

### shutil模块

#### copy()

功能：复制文件

格式：shutil.copy('来源文件','目标地址')

返回值：复制之后的路径

拷贝文件和权限

#### copy2()

功能：复制文件，保留元数据

格式：shutil.copy2('来源文件','目标地址')

返回值：复制之后的路径

拷贝文件和状态信息

#### copyfileobj()

将一个文件的内容拷贝的另外一个文件当中

格式：shutil.copyfileobj(open(来源文件,'r'),open（'目标文件','w'）)

返回值：无

#### copyfile()

功能：将一个文件的内容拷贝的另外一个文件当中

格式:shutil.copyfile(来源文件,目标文件)

返回值：目标文件的路径

（copyfile只拷贝文件内容）

#### copytree()

功能：复制整个文件目录

格式:shutil.copytree(来源目录,目标目录)

返回值：目标目录的路径

注意：无论文件夹是否为空，均可以复制，而且会复制文件夹中的所有内容

#  后面的目标路径必须没有，否则报错,ignore 后的shutil.ignore_patterns()中的参数用正则表达式

#### copymode()

.copymode(src, dst)

功能：拷贝权限

（前提是dst文件存在，不然报错）

#### copystat()

功能：拷贝元数据（状态）

#### rmtree()

功能：移除整个目录，无论是否空

格式：shutil.rmtree(目录路径)

返回值：无

注意：#删除时包括外层的文件夹

#### move()

功能：移动文件或者文件夹

格式：shutil.move(来源地址,目标地址)

返回值：目标地址

which() 功能：检测命令对应的文件路径 格式：shutil.which(‘命令字符串’) 返回值：命令文件所在位置 注意：window和linux不太一样。 window的命令都是.exe结尾，linux则不是 disk_usage()

功能：检测磁盘使用信息

格式：disk_usage(‘盘符’)

返回值：元组
> 元组（tuple）是关系数据库中的基本概念，关系是一张表，表中的每行（即数据库中的每条记录）就是一个元组，每列就是一个属性。 在二维表里，元组也称为行。
