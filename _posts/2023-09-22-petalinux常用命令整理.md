---
layout:     post
title:      "petalinux常用命令"
subtitle:   " 持续更新 "
date:       2023-09-22
author:     "Kev.L"
header-img: "img/picture-of-game/circuit/Sign_Template_fee_schemes_chips_tech_circuit_circuits_dark_1920x1080.jpg"
catalog: true
tags:
    - zynq
---


vi /etc/samba/smb.conf
设置 Samba 用户和密码，命令 smbpasswd -a root 接着按提示输入密码liangkaiwen
重新启动 samba 服务，命令/etc/init.d/samba restart
查看 Ubuntu ip 地址，命令 ifconfig
sudo service nmbd restart
sudo service smbd restart


sudo apt-get install vsftpd
等待软件自动安装，安装完成以后使用如下VI命令打开/etc/vsftpd.conf，命令如下： sudo vi /etc/vsftpd.conf
打开以后vsftpd.conf文件以后找到如下两行： local_enable=YES write_enable=YES
使用如下命令重启 FTP 服务：
sudo /etc/init.d/vsftpd restart



sudo ./vmware-install.pl


sudo apt-get install tftp-hpa tftpd-hpa
sudo mkdir -p /tftpboot 
sudo chmod 777 /tftpboot

最后配置tftp。打开/etc/default/tftpd-hpa文件，将其内容修改如下： 示例代码/etc/default/tftpd-hpa文件内容 
1 # /etc/default/tftpd-hpa 
2 
3 TFTP_USERNAME="tftp" 
4 TFTP_DIRECTORY="/tftpboot" 
5 TFTP_ADDRESS=":69" 
6 TFTP_OPTIONS="-l -c -s"

sudo service tftpd-hpa restart

sudo apt-get install openssh-server

在安装 Petalinux 之前我们需要为 Ubuntu 系统安装一些必要的运行软件以及依赖库，所
以大家需要确保 Ubuntu 能够正常上网，打开 Ubuntu Terminal 终端执行以下命令：
sudo apt-get install tofrodos iproute2 gawk gcc g++ git make net-tools libncurses5-dev \
tftpd zlib1g:i386 libssl-dev flex bison libselinux1 gnupg wget diffstat chrpath socat \
xterm autoconf libtool tar unzip texinfo zlib1g-dev gcc-multilib build-essential \
libsdl1.2-dev libglib2.0-dev screen pax gzip automake
安装 Petalinux 就要考虑安装位置了，对于 Petalinux 这种体积庞大的工具，我们将其放
在 /opt 目 录 下 。 在 /opt 目 录 下 新 建 专 门 存 放 Petalinux 的 文 件 夹 ， 如
/opt/pkg/petalinux/2018.3，在终端输入以下命令即可：
sudo chown -R $USER:$USER /opt
mkdir -p /opt/pkg/petalinux/2018.3

./petalinux-v2018.3-final-installer.run /opt/pkg/petalinux/2018.3


echo "alias sptl='source $PETALINUX/settings.sh'" >> ~/.bashrcecho "alias sptl='source $PETALINUX/settings.sh'" >> ~/.bashrc


1. 通过Vivado创建硬件平台，得到hdf硬件描述文件；
2. 运行source <petalinux安装路径>/settings.sh，设置Petalinux运行环境
3. 通过 petalinux-create -t project 创建petalinux工程；
4. 使用 petalinux-config --get-hw-description，将hdf文件导入到petalinux工程当中并配置petalinux工程；
5. 使用 petalinux-config -c kernel 配置Linux内核；
6. 使用 petalinux-config -c rootfs 配置Linux根文件系统；
7. 配置设备树文件；project-spec/meta-user/recipes-bsp/device-tree/files/system-user.dtsi
8. 使用 petalinux-build  编译整个工程；（必须切换到liangkaiwen用户）
9. 使用 petalinux-package --boot 制作BOOT.BIN启动文件；（必须切换到boot）。petalinux-package --boot --fsbl --fpga --u-boot --kernel --force
10. 制作SD启动卡，将 BOOT.BIN 和 image.ub 以及根文件系统部署到SD卡中；
11. 将SD卡插入开发板，并将开发板启动模式设置为从SD卡启动；
12. 开发板连接串口线并上电启动，串口上位机打印启动信息，登录进入Linux系统。

liangkaiwen@liangkaiwen-virtual-machine:~/petalinux/OPEN-ZYNQ$ cat build/build.log
[INFO] building project
[INFO] sourcing bitbake
environment: 行 224: /opt/pkg/petalinux/2018.3/components/yocto/source/arm/environment-setup-cortexa9hf-neon-xilinx-linux-gnueabi: 没有那个文件或目录
environment: 行 225: /opt/pkg/petalinux/2018.3/components/yocto/source/arm/layers/core/oe-init-build-env: 没有那个文件或目录
ERROR: Failed to source bitbake
ERROR: Failed to build project

cp -r /opt/pkg/petalinux/2018.3/components/yocto/source/  /opt/pkg/petalinux/2018.3/components/yocto/source/arm/

sudo dpkg-reconfigure dash
source /opt/pkg/petalinux/2018.3/settings.sh
创建petalinux工程
petalinux-create -t project --template zynq -n ALIENTEK-ZYNQ
配置petalinux工程
cd ALIENTEK-ZYNQ //进入到petalinux工程目录下 
执行之前，钱换到普通用户
petalinux-config --get-hw-description ../hdf/Phosphor_7020.sdk/ 
//导入hdf文件

petalinux-config -c kernel



petalinux-build
该命令将生成设备树DTB文件、fsbl文件、U-Boot文件，Linux内核和根文件系统映像。编译完成后，生成的映像将位于工程的images目录下。需要说明的是fsbl、U-Boot这两个我们在工程中并没有配置，这是因为Petalinux会根据hdf文件和6.2.4节的配置petalinux工程自动配置fsbl和uboot，如无特需要求，不需要再手动配置。

制作BOOT.BIN启动文件
Petalinux提供了petalinux-package命令将PetaLinux项目打包为适合部署的格式，其中“petalinux-package --boot”命令生成可引导映像，该映像可直接与Zynq系列设备（包括Zynq-7000和Zynq UltraScale + MPSoC）或基于MicroBlaze的FPGA设计一起使用。对于Zynq系列设备，可引导格式为BOOT.BIN，可以从SD卡引导。对于基于MicroBlaze的设计，默认格式为MCS PROM文件，适用于通过Vivado或其他PROM编程器进行编程。
ZYNQ的启动文件BOOT.BIN一般包含fsbl文件、bitstream文件和uboot文件。使用下面命令可生成BOOT.BIN文件：
petalinux-package --boot --fsbl --fpga --u-boot --kernel --force

制作SD启动卡
umount /dev/sdb* 
sudo fdisk /dev/sdb

sudo mkfs.vfat -F 32 -n boot /dev/sdb1 
sudo mkfs.ext4 -L rootfs /dev/sdb2


将petalinux工程目录image/linux目录下的BOOT.BIN和image.ub文件拷贝到名为boot的分区也即/dev/sdb1分区中。以及根文件系统
cp images/linux/BOOT.BIN /media/liangkaiwen/boot

开机自启动测试
/etc/init.d/rcS


********************************************************************************************************
驱动模块的加载和卸载
21.2.1
Linux 驱动有两种运行方式，第一种就是将驱动编译进 Linux 内核中，这样当 Linux 内核
启动的时候就会自动运行驱动程序。第二种就是将驱动编译成模块(Linux 下模块扩展名
为.ko)，在 Linux 内核启动以后使用“insmod”命令加载驱动模块。在调试驱动的时候一般都
选择将其编译为模块，这样我们修改驱动以后只需要编译一下驱动代码即可，不需要编译整个
Linux 代码。而且在调试的时候只需要加载或者卸载驱动模块即可，不需要重启整个系统。总
之，将驱动编译为模块最大的好处就是方便开发，当驱动开发完成，确定没有问题以后就可以
将驱动编译进 Linux 内核中，当然也可以不编译进 Linux 内核中，具体看自己的需求。
模块有加载和卸载两种操作，我们在编写驱动的时候需要注册这两种操作函数，模块的加
载和卸载注册函数如下：
module_init(xxx_init); //注册模块加载函数
module_exit(xxx_exit); //注册模块卸载函数

驱动编译完成以后扩展名为.ko，有两种命令可以加载驱动模块：insmod 和 modprobe，
insmod 是最简单的模块加载命令，此命令用于加载指定的.ko 模块，比如加载 drv.ko 这个驱
动模块，命令如下：
insmod drv.ko
insmod 命令不能解决模块的依赖关系，比如 drv.ko 依赖 first.ko 这个模块，就必须先
使用 insmod 命令加载 first.ko 这个模块，然后再加载 drv.ko 这个模块，但是 modprobe 就不
会存在这个问题。modprobe 会分析模块的依赖关系，然后将所有依赖的模块都加载到内核中，
因此 modprobe 命令相比 insmod 要智能一些。
驱动模块的卸载使用命令“rmmod”即可，比如要卸载 drv.ko，使用如下命令即可：
rmmod drv.ko
也可以使用“modprobe -r”命令卸载驱动，比如要卸载 drv.ko，命令如下：
modprobe -r drv.ko
使用 modprobe 命令可以卸载掉驱动模块所依赖的其他模块，前提是这些依赖模块已经没
有被其他模块所使用，否则就不能使用 modprobe 来卸载驱动模块。所以对于模块的卸载，还
是推荐使用 rmmod 命令。


运行测试
23.6.2 将上一小节编译出来的 newchrled.ko 和 ledApp 这两个文件拷贝到开发板根文件系统
/lib/modules/4.14.0-xilinx 目录下，重启开发板，进入到/lib/modules/4.14.0-xilinx 目 录中，
输入如下命令加载 newchrled.ko 驱动模块：
Depmod //第一次加载驱动的时候需要运行此命令
modprobe newchrled.ko //加载驱动
驱动加载成功以后会输出申请到的主设备号和次设备号



mkdir -p ~/work/linux-4.14 //创建目录
tar -xzf alientek-linux-4.14.0-xlnx-v2018.3.tar.gz -C ~/work/linux-4.14/ //解压
sync //同步
rm -rf alientek-linux-4.14.0-xlnx-v2018.3.tar.gz //删除压缩包文件

接下来再将 U-Boot 源码拷贝到 Ubuntu 系统中，同样这里也是使用正点原子针对于启明星
开发板进行移植、修改过的 U-Boot 源码包，而不再使用 petalinux 默认的 U-Boot 源码，在开
发板资料包：ZYNQ 开发板资料盘(A 盘)\4_SourceCode\3_Embedded_Linux\资源文件\uboot 目
录下也有两个 U-Boot 源码压缩包文件，分别是 u-boot-xlnx-xilinx-v2018.3.tar.gz 和
alientek-uboot-2018.01-xlnx-v2018.3.tar.gz，同理 u-boot-xlnx-xilinx-v2018.3.tar.gz
是 Xilinx 官方提供的原版 U-Boot 源码包，而 alientek-uboot-2018.01-xlnx-v2018.3.tar.gz
便 是 正 点 原 子 针 对 于 启明星 开 发 板 进 修 改 过 的 U-Boot 源码包 ， 这 里 我 们 将
alientek-uboot-2018.01-xlnx-v2018.3.tar.gz 压缩包文件拷贝到 Ubuntu 系统中，如下所示
mkdir ~/work/uboot-2018.01
tar -xzf alientek-uboot-2018.01-xlnx-v2018.3.tar.gz -C ~/work/uboot-2018.01/
sync
rm -rf alientek-uboot-2018.01-xlnx-v2018.3.tar.gz


在每个使用 Petalinux 或 arm 交叉编译器 arm-linux-gnueabihf-的终端都需
要先设置 Petalinux 的环境变量，如何设置，见 5.4 节设置 Petalinux 环境变量。如果只需要
arm 的交叉编译器，不需要 Petalinux，可以在终端输入如下命令：
sptl
echo 'export PATH=$PATH:'`which arm-linux-gnueabihf-gcc | xargs dirname` | tee -a ~/.bashrc
输出结果： export PATH=$PATH:/opt/pkg/petalinux/2018.3/tools/linux-i386/gcc-arm-linux-gnueabi/bin
这样设置以后，我们就可以直接使用 arm 的交叉编译器，无需再设置 Petalinux 的工作环
境。


cd /mnt/hgfs/share_dir/4_SourceCode/3_Embedded_Linux/Linux驱动例程/



/************************************20230908***********************************************/
petalinux编译过程
0.创建 Vivado 硬件平台
system_wrapper.xsa拷贝到 petalinux/xsa_7020 目录
1.设置 Petalinux 环境变量
source /opt/pkg/petalinux/2020.2/settings.sh
#或者
sptl
2.创建 petalinux 工程
petalinux-create -t project --template zynq -n ALIENTEK-ZYNQ
-t 是“--type”的简写。 template 参数表明创建的 petalinux 工程使用的平台模板，此处的
zynq 表明使用的是 zynq 平台模板的 petalinux 工程，用于 zynq-7000 系列的芯片。 name 参数
（此处简写为“-n”）后接的是 petalinux 工程名，如此处的“ALIENTEK-ZYNQ”。 
3.配置 petalinux 工程
cd ALIENTEK-ZYNQ
petalinux-config --get-hw-description ../xsa_7020/
保持默认
4.配置 Linux 内核
petalinux-config -c kernel
5.配置 Linux 根文件系统
petalinux-config -c rootfs
6.配置设备树文件
编辑当前 petalinux 工程目录下的
project-spec/meta-user/recipes-bsp/device-tree/files/system-user.dtsi 文件
7.编译 Petalinux 工程
petalinux-build
8.制作 BOOT.BIN 启动文件
petalinux-package --boot --fsbl --fpga --u-boot --force
9.制作 SD 启动卡
将该工程 image/linux 目录下的 BOOT.BIN、 boot.scr 和 image.ub 文件拷贝到
名为 boot 的分区也即/dev/sdb1 分区,注意dev挂载路径为/media/...



/*********************************0912********************************/
测试显示设备
DRM 是 Linux 下的图形显示架构， libdrm 是 DRM 架构下的接口库，为上层应用程序提
供 API 接口。 本小节使用 libdrm 库提供的“modetest”测试工具。
运行下面命令测试 LCD 屏显示：
modetest -D amba_pl:drm_pl_disp_lcd -s 35@33:1024x600@BG24
上面命令中参数 D 表示设备，参数 s 格式为<connector_id>@<crtc_id>:<mode>@<format>，
这些参数可以通过“modetest -D amba_pl:drm_pl_disp_lcd -c”命令查询。

对于 ZYNQ7020 开发板，可以运行下面两个命令测试 HDMI 显示：
modetest -D amba_pl:drm_pl_disp_hdmi -s 35@33:1920x1080@BG24 -P 32@33:1920x1080
或者
modetest -D amba_pl:drm_pl_disp_hdmi -s 35@33:1280x720@BG24 -P 32@33:1280x720
分别是 HDMI 的 1080P 和 720P 显示， 同样可以看到 HDMI 显示器能显示彩条




book@book-virtual-machine:~/phased_array/xsa_7020$ git commit -am '正点原子默认xsa文件'



生成 BOOT.BIN
运行如下命令编译 fsbl 和 uboot：
petalinux-build -c bootloader
petalinux-build -c u-boot
然后执行下面命令生成 BOOT.bin：
petalinux-package --boot --fsbl --u-boot --dtb no --force


Linux开发指南V2.1第56章USB WiFi驱动、文件系统
root@zynq_lcd_test:~# cat /etc/wpa_supplicant.conf
ctrl_interface=/var/run/wpa_supplicant
ap_scan=1
network={
ssid="TP-LINK_OPEN_1"
psk="open1234567"
}
wpa_supplicant -D wext -c /etc/wpa_supplicant.conf -i wlan0 &
udhcpc -i wlan0 //自动获取 IP 地址

/*******************ssh发送文件到板子上*******************************/
scp /home/book/workspace/interp_tes/Debug/interp_tes.elf root@192.168.1.223:/home/root/



arm-none-linux-gnueabihf-gcc  -o lcdTest lcdTest.c
/*上传代码*/
ssh-keygen -t rsa -b 4096 -C "1303985440@qq.com"
cat ~/.ssh/id_rsa.pub

git branch -M main
git remote add origin git@github.com:kaiwenll/b_scan.git
git push -u origin main
/*********************************QT**********************************/

使用新的根文件系统启动开发板，进入系统之后，我们需要设置 Qt 运行的环境变量，运行下面这些命
令导出环境变量，如下所示：
export QT_QPA_PLATFORM=linuxfb
export QT_QPA_GENERIC_PLUGINS=tslib:/dev/input/event1

root@zynq_lcd_test:~# env
SHELL=/bin/sh
EDITOR=vi
PWD=/home/root
LOGNAME=root
HOME=/home/root
QT_QPA_PLATFORM=linuxfb
TERM=vt102
USER=root
SHLVL=1
PS1=\u@\h:\w\$
HUSHLOGIN=FALSE
PATH=/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/sbin:/sbin
MAIL=/var/spool/mail/root
QT_QPA_GENERIC_PLUGINS=tslib:/dev/input/event1
_=/usr/bin/env
root@zynq_lcd_test:~#





/**编译uboot--2018版*/
make distclean // 清理工程
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- atk_7020_defconfig // 配置(7010 核心板对
应的配置文件是 atk_7010_defconfig， 7020 核心板对应的配置文件是 atk_7020_defconfig)
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- all -j16
