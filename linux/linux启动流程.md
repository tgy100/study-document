### 一.系统概述

#### 1) Linux系统的组成部分

​	内核+根文件系统

##### (1) 内核的作用

​	进程管理、内存管理、网络协议栈、文件系统、驱动程序、安全功能

#### 2) 运行中的系统环境可分为两层

​	内核空间、用户空间

##### (1)用户空间

​	应用程序(进程或线程)

##### (2)内核空间: 

​	内核代码(系统调用)

#### 3) 内核设计流派

##### (1)单内核设计:

​	把所有功能集成于同一个程序, linux

##### (2)微内核设计

​	每种功能使用一个单独的子系统实现，Windows, Solaris

##### (3)Linux内核特点:

###### ①.支持模块化

​	 .ko (kernel object)

###### ②.支持模块运行时动态装载或卸载;

###### ③.组成部分:

- 核心文件: /boot/vmlinuz-VERSION-release

- ramdisk :

  ramDisk实际上是从**内存中划出一部分作为一个分区使用**，换句话说，就是把**内存一部分当做硬盘使用**，你可以向里边存文件。

  - CentOS 5 : /boot/initrd-VERSION-release.img

  - CentOS 6,7 : /boot/initramfs-VERSION-release.img

- 模块文件: /lib/modules/VERSION-release

#### 4)运行级别

​	为了系统的运行或维护等目的而设定的机制;

##### (1) 0-6 : 7个级别;

- 0 关机, shutdown
- 1 单用户模式(single user) , root用户,无须认证;维护模式;
- 2 多用户模式(multi user) ,会启动网络功能,但不会启动NFS ;维护模式;
- 3 多用户模式(mutll user) ,完全功能模式;文本界面;
- 4  预留级别:目前无特别使用目的,但习惯以同3级别功能使用;
- 5 多用户模式(multi user)，完全功能模式,图形界面;
- 6、重启, reboot
  默认级别: 3,5

##### (2)级别切换: 

​	init #

##### (3) 级别查看:

​	who -r
​	runlevel

##### (4) 配置文件	

​	/etc/inittab

1. 每行定义一种action以及与之对应的process

   id:runlevels:action:process

   - id :一个任务的标识符;
   - runlevels :在哪些级别启动此任务; # , ### ,也可以为空,表示所有级别;
   - action :在什么条件下启动此任务;
     - wait :等待切换至此任务所在的级别时执行一次;
     - respawn :一旦此任务终止,就自动重新启动之;
     - initdefault :设定默认运行级别;此时, process省略;
     - sysinit :设定系统初始化方式,此处一般为指定/etc/rc.d/rc.sysinit脚本 ;
   - process :任务;


   **例如:**

   ```shell
   ld:3:initdefault:
   #每个运行级别都使用rc.sysinit 
   sl::sysinit:/etc/rc.d/rc.sysinit
   l0:0:wait:/etc/rc.d/rc 3
   ```

2. /etc/rc.d/rc 脚本解释

   ```shell
   # First, run the KILL scripts.
   for i in /etc/rc$runlevel.d/K* ; do
   
           # Check if the subsystem is already up.
           subsys=${i#/etc/rc$runlevel.d/K??}
           [ -f /var/lock/subsys/$subsys -o -f /var/lock/subsys/$subsys.init ] || continue
           check_runlevel "$i" || continue
   
           # Bring the subsystem down.
           [ -n "$UPSTART" ] && initctl emit --quiet stopping JOB=$subsys
           $i stop
           [ -n "$UPSTART" ] && initctl emit --quiet stopped JOB=$subsys
   done
   
   for i in /etc/rc$runlevel.d/S* ; do
   
           # Check if the subsystem is already up.
           subsys=${i#/etc/rc$runlevel.d/S??}
           [ -f /var/lock/subsys/$subsys ] && continue
           [ -f /var/lock/subsys/$subsys.init ] && continue
           check_runlevel "$i" || continue
   
           # If we're in confirmation mode, get user confirmation
           if [ "$do_confirm" = "yes" ]; then
                   confirm $subsys
                   rc=$?
                   if [ "$rc" = "1" ]; then
                           continue
                   elif [ "$rc" = "2" ]; then
                           do_confirm="no"
                   fi
           fi
   
           update_boot_stage "$subsys"
           # Bring the subsystem up.
           [ -n "$UPSTART" ] && initctl emit --quiet starting JOB=$subsys
           if [ "$subsys" = "halt" -o "$subsys" = "reboot" ]; then
                   export LC_ALL=C
                   exec $i start
           fi
           $i start
           [ -n "$UPSTART" ] && initctl emit --quiet started JOB=$subsys
   done
   ```

   意味着去启动或关闭/etc/rc.d/rc3.d/目录下的服务脚本所控制服务;
   K\*:要停止的服务;K##*,优先级,数字越小,越是优先关闭;依赖的服务先关闭,而后关闭被依赖的;
   S\*:要启动的服务;S##\*,优先级,数字越小,越是优先启动;被依赖的服务先启动,而依赖的服务后启动;
   rc脚本:接受一个运行级别数字为参数;

### 二. centos 6启动流程

#### 1) POST :加电自检

​	这个过程是开机后，BIOS或UEFI进行硬件检查的阶段

- ROM : CMOS
  - BIOS : Basic Input and Output System
- ROM+ RAM 

#### 2) MBR引导

​	自检硬件没有问题时候，这里以BIOS为例，BIOS将会直接去找硬盘的第一个扇区，找到前446字节，将MBR加载到内存中，MBR将告诉程序下一阶段去哪里找系统grub引导。此阶段属于grub的第一阶段。grub还有1.5阶段和2阶段。

#### 3) GRUB引导

​	grub第1.5和2阶段，信息默认存放在扇区中,如果使用grub-install生成的2阶段的文件是存放在/boot分区中的。 

​	为了加载内核系统，不得不加载/boot分区，而加载/boot分区，需要有/boot分区的驱动，/boot分区驱动是放在/boot分区中的,啊，我们好像进入了死循环了，Linux是怎么解决的呢？就是靠放在1.5阶段中的数据，是放在第一个扇区后的后续扇区中，具体占用多少字节，不太清楚，只知道1.5阶段和2阶段总共27个扇区。

##### (1)stage1.5：

​	mbr之后的扇区，识别stage2所在的分区上的文件系统

##### (2) stage2：

​	开机启动的时候看到的Grub选项、信息，还有修改GRUB背景等功能都是stage2提供的，stage2会去读入/boot/grub/grub.conf或者menu.lst等配置文件

#### 4 )读取grub.conf文件

​	读取grub.conf文件以确定内核启动的参数，准备启动内核

#### 5) 启动内核

​	加载内核，核心开始解压缩，启动一些最核心的程序。 
​	因为为了让内核足够轻小，硬件驱动并没有放在内核文件里面，我们可以看到内核很小,才4M左右，我们可以想象Windows中的驱动，安装系统时候还需要使用驱动软件下载好长时间呢 ,因此需要使用/initramfs-2.6.32-696.el6.x86_64.img来驱动硬件

#### 6)加载伪文件系统（ramdisk），

​	内核已将启动起来了，再调用ramdisk文件，尝试驱动所有的硬件设备，到这一步，内核起来了，所有驱动也装上了，因此后面的启动就可以交给程序了

#### 7)启动init进程

grub中默认指定init=/sbin/init程序，可以在grub.conf中kernel行自定义执行程序init=/bin/bash,此时可以绕过下面步骤直接进入bash界面。 

内核源代码文件中显示996行左右，规定了init启动的顺序，/sbin/init->/etc/init->/bin/init->/bin/sh,/bin/bash没有写，应该是和/bin/sh一样吧。

### 三.grub2学习

