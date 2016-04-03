---
title: 误删libc.so.6之后
layout: post
tags:
  - linux
  - daily
---

前两天手残误删了服务器上的libc.so.6,起因是因为跑程序需要更高版本的libc,看它只是个软连接,就打算删了再连接到更高版本,万万没想到删了之后嘛都干不了了,一查才发现它这么重要:
> Any Unix-like operating system needs a C library:the library which defines the system calls and other basic facilities such as open,malloc,printf,exit...The GNU C Library is used as the C library in the GNU systems and most systems with the Linux kernel.

libc封装了linux底层的API和很多c的库函数,所以删了之后很多命令都不能用了.然后机器挂了今天被叫去东校机房修ToT.

其实如果本来是root用户,删掉之后直接执行`LD_PRELOAD=/lib64/libc-2.12.so ln -s /lib64/libc-2.12.so /lib64/libc.so.6`就好, LD_PRELOAD允许定义程序运行前优先加载的动态链接库,所以这个使用命令即使找不到`libc.so.6`也能在`libc.so.6`链接到的`libc-2.12.so`中找到需要的函数,所以有root权限的话就可以执行了.但是无奈我当时不是root账户,su,sudo又不能执行了,只能到机房从u盘进入救援模式修改.

服务器原来的系统是Redhat 6,u盘装的是CentOS 6,一开始进入不了系统,卡在硬件检查,查了下启动时加上参数`acpi=off`,就好:
> ACPI (Advanced Configuration and Power Interface) is a standard for handling power management.Older systems may not support ACPI full,so sometimes it helps to give the kernel a hint to not use it."acpi=off"

进入救援模式后创建了`libc.so.6`结果还是开不了机,折腾半天才发现原来u盘的系统是32位的,改成64位的就好了.猜测原因可能是这样:32位的系统执行`chroot /mnt/sysimage`失败,因为原系统中`chroot`对应的二进制文件是64位的,所以无法在32位系统执行,所以之后执行的所以指令都是32位的,用32位的`ln`指令创建的软连接可能64位系统并不能识别.所以还是认为没有`libc.so.6`.

折腾了一下午终于解决,没文化真可怕,今后用`sudo`时可要谨慎再谨慎.之前实习时也弄坏过一次系统,这次好像三台服务器都坏掉了,只有我主动承认了,其他两个现在还不知道谁搞的ToT.服务器的用户管理真是大问题,给了权限容易经常挂掉,不给又要经常找管理员,并且在学校也没有专职的管理员,不知道现在有没有比较好的解决方案.

参考:

- [glibc作用](http://blog.chinaunix.net/uid-26959771-id-4113388.html)
- [警惕UNIX下的LD_PRELOAD环境变量](http://blog.csdn.net/haoel/article/details/1602108)
- [What do the different Boot Options mean? (i.e. acpi=off, noapic, nolapic, etc)](http://askubuntu.com/questions/52096/what-do-the-different-boot-options-mean-i-e-acpi-off-noapic-nolapic-etc)
- [从一次生产事故说起——linux的单用户模式，救援模式等等，事故linux](http://www.bkjia.com/xtzh/887510.html)