updater-script简介：
    updater-script是我们升级时所具体使用到的脚本文件，它主要用以控制升级流程的主要逻辑。具体位置位于更新包中/META-INFO/com/google/android/目录下，在我们制作升级包的时候产生。

updater-script生成：
  那么升级脚本updater-script是如何产生的呢，我们来看ota_from_target_file中的一条语句，这个之前有过介绍。

  #在/build/tools/releasetools/目录下的模块edify_generator作为一个抽象的脚本生成器，用来生成edify脚本。这里的edify脚本指的就是updater-script-升级安装脚本，它是一个文本文件。而edify是用于从.zip文件中安装CyanogenMod和其它软件的简单脚本语言。edify脚本不一定是用于更新固件。它可以用来替换/添加/删除特定的文件，甚至格式分区。通常情况下，edify脚本运行于用户在恢复模式中选择“刷写zip”时。
  script = edify_generator.EdifyGenerator(3, OPTIONS.info_dict)

那么updater-script中如何自动一步一步生成每一个脚本指令或者脚本语句呢，这里我们简单举几个例子给大家作为一个参考：

①对比更新包时间戳，只允许升级旧版本

下面这段代码是在ota_from_target_files中的语句

  #下面这段代码我们可以理解为不允许降级，也就是说在脚本中的这段Assert语句，使得update zip包只能用于升级旧版本。
  if not OPTIONS.omit_prereq:
    ts = GetBuildProp("ro.build.date.utc", OPTIONS.info_dict)#得到系统编译世界时间
    ts_text = GetBuildProp("ro.build.date", OPTIONS.info_dict)#得到编译日期
    script.AssertOlderBuild(ts, ts_text)
下面是对应edify_generator模块的中的相关方法：
  def AssertOlderBuild(self, timestamp, timestamp_text):
    """Assert that the build on the device is older (or the same as)
    the given timestamp."""
    self.script.append(
        ('(!less_than_int(%s, getprop("ro.build.date.utc"))) || '
         'abort("Can\'t install this package (%s) over newer '
         'build (" + getprop("ro.build.date") + ").");'
         ) % (timestamp, timestamp_text))
那么我们从上面的代码中可以看到，这里呢在updater-script中追加了这样的一段语句：
'(!less_than_int(%s, getprop("ro.build.date.utc"))) || '
         'abort("Can\'t install this package (%s) over newer '
         'build (" + getprop("ro.build.date") + ").");'
         ) % (timestamp, timestamp_text)
那么实际在脚本中是这样子的，如：

(!less_than_int(1413536309, getprop("ro.build.date.utc"))) || abort("Can't install this package (Fri Oct 17 16:58:29 CST 2014) over newer build (" + getprop("ro.build.date") + ").");
那么这段话我们在执行updater-script脚本的时候具体逻辑是指当更新包中版本低于当前手机中的版本时终止安装更新。那么在安装的时候具体如何操作的呢，我们随后再详细描述。
②显示进度

  #在升级脚本中生成显示进度的语句，这里实际上是在脚本中增加了这样一条语句（show_progress） 参数一表示将暂用的时间在总体时间的比例。参数二用于控制显示速度。如show_progress(0.1, 10);show_progress下面的操作可能进行10s，完成后进度条前进0.1（也就是10%）
  script.ShowProgress(0.5, 0)
那么这里调用了edify_generator模块中的ShowProgress(0.05,0)：
 def SetProgress(self, frac):
    """Set the position of the progress bar within the chunk defined
    by the most recent ShowProgress call.  'frac' should be in
    [0,1]."""
    self.script.append("set_progress(%f);" % (frac,))
而实际在updater-script中的逻辑如下：

show_progress(0.500000, 0);
③格式化分区

  #如果命令中有参数要求擦除data分区数据，那么在升级脚本中生成格式化分区的语句，分区用挂载点来描述，格式如下：（/data）
  if OPTIONS.wipe_user_data:
    script.FormatPartition("/data")
那么这里调用了edify_generator模块中的FormatPartition（）函数：

  def FormatPartition(self, partition):
    """Format the given partition, specified by its mount point (eg,
    "/system")."""
 
    reserve_size = 0
    fstab = self.info.get("fstab", None)
 
    #wschen 2012-11-12 
    if partition == "/custom":
      self.script.append('format("ext4", "EMMC", "/dev/block/mmcblk", "0", "/custom");')
 
    elif fstab:
      p = fstab[partition]
      self.script.append('format("%s", "%s", "%s", "%s", "%s");' %
                         (p.fs_type, common.PARTITION_TYPES[p.fs_type],
                          p.device, p.length, p.mount_point))
④挂载分区

script.Mount("/system")
那么这里调用了edify_generator模块中的Mount（）函数：
  def Mount(self, mount_point):
    """Mount the partition with the given mount_point."""
	#根据指定的挂载点挂载分区
    fstab = self.info.get("fstab", None)
 
    #wschen 2012-11-12 
	#这里挂载定制分区custom，暂不作详细描述，随后会针对升级定制分区进行一个详细的介绍
    if mount_point == "/custom":
      self.script.append('mount("ext4", "EMMC", "/dev/block/mmcblk", "/custom");')
      self.mounts.add(mount_point)
 
    elif fstab:
      p = fstab[mount_point]
      self.script.append('mount("%s", "%s", "%s", "%s");' %
                         (p.fs_type, common.PARTITION_TYPES[p.fs_type],
                          p.device, p.mount_point))
      self.mounts.add(p.mount_point)
⑤释放文件夹内容到指定文件夹下，注意这里被释放的文件夹是指在OTA package中也就是更新包中的对应的文件夹，也就是下面函数中的第一个参数

    script.UnpackPackageDir("recovery", "/system")
    script.UnpackPackageDir("system", "/system")
那么这里调用了edify_generator模块中的UnpackPackageDir()函数
  def UnpackPackageDir(self, src, dst):
    """Unpack a given directory from the OTA package into the given
    destination directory."""
    self.script.append('package_extract_dir("%s", "%s");' % (src, dst))
⑥建立符号链接

symlinks = CopySystemFiles(input_zip, output_zip)
    script.MakeSymlinks(symlinks)
edify_generator模块中相对应的MakeSymlinks()函数
  def MakeSymlinks(self, symlink_list):
    """Create symlinks, given a list of (dest, link) pairs."""
    by_dest = {}
    for d, l in symlink_list:
      by_dest.setdefault(d, []).append(l)
 
    for dest, links in sorted(by_dest.iteritems()):
      cmd = ('symlink("%s", ' % (dest,) +
             ",\0".join(['"' + i + '"' for i in sorted(links)]) + ");")
      self.script.append(self._WordWrap(cmd))

⑦权限设置
          script.SetPermissions("/"+item.name, item.uid, item.gid,
                                item.mode, item.selabel, item.capabilities)

具体函数如：
  def SetPermissions(self, fn, uid, gid, mode, selabel, capabilities):
    """Set file ownership and permissions."""
    if not self.info.get("use_set_metadata", False):
      self.script.append('set_perm(%d, %d, 0%o, "%s");' % (uid, gid, mode, fn))
    else:
      if capabilities is None: capabilities = "0x0"
      cmd = 'set_metadata("%s", "uid", %d, "gid", %d, "mode", 0%o, ' \
          '"capabilities", %s' % (fn, uid, gid, mode, capabilities)
      if selabel is not None:
        cmd += ', "selabel", "%s"' % ( selabel )
      cmd += ');'
      self.script.append(cmd)
⑧写入分区

  script.WriteRawImage("/boot", "boot.img")
具体相对应函数如下：
  def WriteRawImage(self, mount_point, fn):
    """Write the given package file into the partition for the given
    mount point."""
 
    fstab = self.info["fstab"]
    if fstab:
      p = fstab[mount_point]
      partition_type = common.PARTITION_TYPES[p.fs_type]
      args = {'device': p.device, 'fn': fn}
      if fn == "boot.img" and p.device == "boot":
        self.script.append(
          ('assert(package_extract_file("%(fn)s", "/tmp/%(fn)s"),\n'
           '       write_raw_image("/tmp/%(fn)s", "bootimg"),\n'
           '       delete("/tmp/%(fn)s"));') % args)
 
      elif partition_type == "MTD":
        self.script.append(
            'write_raw_image(package_extract_file("%(fn)s"), "%(device)s");'
            % args)
      elif partition_type == "EMMC":
        self.script.append(
            'package_extract_file("%(fn)s", "%(device)s");' % args)
      else:
        raise ValueError("don't know how to write \"%s\" partitions" % (p.fs_type,))
下面贴出updater-script简单语法供大家参考：

1、mount
语法：
mount(type, location, mount_point);这里表示挂载，其中type="MTD" location="<partition>" 挂载yaffs2文件系统分区；
type="vfat" location="/dev/block/<whatever>" 挂载设备。如：mount("MTD", "system", "/system");挂载system分区，设置返回指针"/system”
mount("vfat", "/dev/block/mmcblk1p2", "/system");
挂载/dev/block/mmcblk1p2，返回指针"/system”
2、Unmount
语法：
unmount(mount_point);指mount_point是mount所设置产生的指针。其作用与挂载相对应，卸载分区或设备。此函数与mount配套使用。如；unmount("/system");
卸载/system分区
3、Format
语法：
format(type, location);这里的type="MTD" location=partition（分区），格式化location参数所代表的分区。如：format("MTD", "system");格式化system分区
4、Delete
语法：
delete(<path>);删除path路径下文件，如delete("/data/zipalign.log");删除文件/data/zipalign.log
5、delete_recursive
语法：
delete_recursive(<path>);删除path路径下文件夹；如：delete_recursive("/data/dalvik-cache");删除文件夹/data/dalvik-cache
6、show_progress
语法：
show_progress(<fraction>,<duration>);这个之前介绍过，用于显示进度条，进度条会根据<duration>进行前进<fraction>，如show_progress(0.1, 10);show_progress下面的操作可能进行10s，完成后进度条前进0.1（也就是10%）
7、package_extract_dir
语法：
package_extract_dir(package_path, destination_path);释放文件夹package_path至destination_path如：package_extract_dir("system", "/system");释放ROM包里system文件夹下所有文件和子文件夹至/system
8、package_extract_file
语法：
package_extract_file(package_path, destination_path);这里表示解压package_path文件至destination_path，如package_extract_dir("my.zip", "/system");解压ROM包里的my.zip文件至/system
9、Symlink
语法：
symlink(<target>, <src1>, <src2>,...);表示建立指向target符号链接src1，src2，……；如symlink("toolbox", "/system/bin/ps");建立指向toolbox的符号链接/system/bin/ps
10、set_perm
语法：
set_perm(<uid>, <gid>,<mode>, <path>);表示设置权限，设置<path>文件的用户为uid，用户组为gid，权限为mode；如：set_perm(1002, 1002, 0440, "/system/etc/dbus.conf");设置文件/system/etc/dbus.conf的所有者为1002，所属用户组为1002，权限为：所有者有读权限，所属用户组有读权限，其他无任何权限。
11、set_perm_recursive
语法：
set_perm_recursive(<uid>,<gid>,<dir-mode>,<file-mode>,<path>);表示设置文件夹和文件夹内文件的权限；如：set_perm_recursive(1000, 1000, 0771, 0644, "/data/app");设置/data/app的所有者和所属用户组为1000，app文件夹的权限是：所有者和所属组拥有全部权限，其他有执行权限；app文件夹下的文件权限是：所有者有读写权限，所属组有读权限，其他有读权限。
12、ui_print
语法：
ui_print("str");这里指屏幕打印输出"str"；如：ui_print("It's ready!");屏幕打印It’s ready!
13、run_program
语法：
run_program(<path>);指运行<path>脚本。如：run_program("/sbin/busybox","mount","/system")；
14、write_raw_image
语法：
write_raw_image(<path>, partition);写入<path>至partition分区；如：write_raw_image("/tmp/boot.img", "boot")将yaffs2格式的boot包直接写入boot分区
15、assert
语法：
assert(<sub1>,<sub2>,<sub3>);这里如果执行sub1不返回错误则执行sub2，如果sub2不返回错误则执行sub3以此类推；如：assert(package_extract_file("boot.img", "/tmp/boot.img"),
write_raw_image("/tmp/boot.img", "boot"),
delete("/tmp/boot.img"));执行package_extract_file，如果不返回错误则执行write_raw_image，如果write_raw_image不出错则执行delete[1] 

16、getprop()

语法：getprop("key")：通过指定key的值来获取对应的属性信息。如：getprop(“ro.product.device”)获取ro.product.device的属性值。
————————————————
版权声明：本文为CSDN博主「叶桐」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/huangyabin001/article/details/43965307
