# linux-notes
此笔记存放移远模块调试记录，linux相关笔记暂存都存在README中，后续内容增多，在区分子笔记本，再在README中汇总

## 相关概念及注意事项
* 移远SDK 压缩包的解压必须在 Ubuntu“普通用户”环境下解压。
* QuecOpenTM是一种以模组作为主处理器的应用方式。采用QuecOpenTM解决方案，可以简化用户对无线应用的开发流程，精简硬件结构设计，从而降低产品成本。
* 所有开发之前，都必须先执行`source ql-ol-crosstool/ql-ol-crosstool-env-init`
* 移远SDK不能放在`VMTOOLS`目录下，不然make的时候会出现错误

## 常用命令
* 每次打开终端都需要初始化交叉编译环境 `source ql-ol-crosstool/ql-ol-crosstool-env-init`
* 进入adb运行了程序，按`ctrl+c`退出

## 自定义的命令
>编辑环境变量`vi /etc/bash.bashrc`,立即生效`source /etc/bash.bashrc`
```
#------------tom define-----------
# 设置快捷命令
alias cptom='cp -r /mnt/hgfs/repository/TP38P-open /root/ql-ol-sdk/open/'
alias cpmcu='cp -r /root/ql-ol-sdk/open/TP38P-open/src /mnt/hgfs/VMTools/'
alias cdtom='cd /root/ql-ol-sdk/open/TP38P-open/src'

#------------tom define end-------
```

## linux APP开发步骤
> 移远SDK以普通用户解压是因为tar命令的要求，以root解压所有权归root，以普通用户解压所有权归普通用户；SDK放在VMTools目录下解压，后面make会出错，可能是因为文件目录过深，makefile为考虑到的问题；
1. 以普通用户`tom`解压移远SDK `tar -jxvf EC20CEHCLGR06A05V03M1G_OCPU_RC.tar.bz2`
2. 初始化交叉编译环境 `source ql-ol-crosstool/ql-ol-crosstool-env-init`
![初始化交叉编译环境](https://github.com/gaozichen2012/linux-notes/blob/master/img/5-1-APP%E5%BC%80%E5%8F%91.jpg)
3. `make`生辰可执行文件，并传给VMTools
![make](https://github.com/gaozichen2012/linux-notes/blob/master/img/5-2-APP%E5%BC%80%E5%8F%91.jpg)
4. 将可执行文件通过adb传到`/usrdata`目录下，修改可执行文件的权限，并进入adb shell执行该文件
![执行](https://github.com/gaozichen2012/linux-notes/blob/master/img/5-3-APP%E5%BC%80%E5%8F%91.jpg)

## bootloader开发步骤（测试正常）
>`make aboot`生成bootloader，`make aboot/clean`删除生成的bootloader及相关文件
1. 使用`make aboot`编译 bootloader, 并在当前路径 target/下生成`appsboot.mbn`
![make aboot](https://github.com/gaozichen2012/linux-notes/blob/master/img/8-1-bootloader.jpg)
![appsboot.mbn](https://github.com/gaozichen2012/linux-notes/blob/master/img/8-2-bootloader.jpg)
2. 替换掉下载包`EC20CEHCLGR06A05V03M1G_TP598\update`中的`appsboot.mbn`
![下载](https://github.com/gaozichen2012/linux-notes/blob/master/img/8-3-bootloader.jpg)
3. 使用移远提供的模块升级工具Quectel_Customer_FW_Download_Tool_V4.32升级

## kernel开发步骤（测试正常）
>`make kernel`生成kernel，`make kernel/clean`删除生成的kernel及相关文件；`make kernel_module`编译内核模块（修改了 kmod 相关代码才需要执行），并自动安装到rootfs 目录下， 需要重新制作 sysfs.ubi（暂时未用到）
1. 使用`make kernel`编译 kernel, 并在当前路径 target/下生成`mdm9607-perf-boot.img`
2. 替换掉下载包`EC20CEHCLGR06A05V03M1G_TP598\update`中的`mdm9607-perf-boot.img`
3. 使用移远提供的模块升级工具Quectel_Customer_FW_Download_Tool_V4.32升级

## 文件系统制作（测试正常）
>`make rootfs`生成sysfs.ubi，`make rootfs/clean`删除生成的sysfs.ubi及相关文件；
1. 使用`make rootfs`编译文件系统, 并在当前路径 target/下生成`mdm9607-perf-sysfs.ubi`
2. 替换掉下载包`EC20CEHCLGR06A05V03M1G_TP598\update`中的`mdm9607-perf-sysfs.ubi`
3. 使用移远提供的模块升级工具Quectel_Customer_FW_Download_Tool_V4.32升级

## usrdata.ubi制作（测试正常）
>Flash 默认有一个 usr_data 分区， 可以用来存放用户的文件和进行 DFOTA 升级使用。
>`make usrdata`生成usrdata.ubi，`make usrdata/clean`删除生成的usrdata.ubi及相关文件；
1. 使用`make usrdata`编译 usrdata, 并在当前路径 target/下生成`usrdata.ubi`
2. 替换掉下载包`EC20CEHCLGR06A05V03M1G_TP598\update`中的`usrdata.ubi`
3. 使用移远提供的模块升级工具Quectel_Customer_FW_Download_Tool_V4.32升级

## 进入和退出adb shell
ubuntu的adb驱动装的有问题，所以使用windows adb
![进入和退出adb shell](https://github.com/gaozichen2012/linux-notes/blob/master/img/1-%E8%BF%9B%E5%85%A5%E9%80%80%E5%87%BAadb%20shell.jpg)

## adb push和pull文件
![adb push](https://github.com/gaozichen2012/linux-notes/blob/master/img/2-adb%20push%E6%96%87%E4%BB%B6.jpg)
![adb pull](https://github.com/gaozichen2012/linux-notes/blob/master/img/3-adb%20pull%E6%96%87%E4%BB%B6.jpg)

## 移远SDK解析
![移远SDK文件夹](https://github.com/gaozichen2012/linux-notes/blob/master/img/4-%E7%A7%BB%E8%BF%9CSDK%E6%96%87%E4%BB%B6%E5%A4%B9.jpg)

| 文件夹 | 说明 |
| --- | --- |
|ql-ol-kernel|Linux 内核源码（根据客户定制权限才开放）|
|ql-ol-bootloader|高通 bootloader 源码（根据客户定制权限才开放）|
|ql-ol-rootfs|平台运行时的根文件系统|
|ql-ol-crosstool|交叉工具链|
|ql-ol-extsdk|包含了一些 API， example 以及 tools 工具包|
|ql-ol-usrdata|用户数据|

## EC20CEHCLGR06A05V03M1G_TP598\update文件解析
>这几个文件都是编译移远SDK生成的文件

![update](https://github.com/gaozichen2012/linux-notes/blob/master/img/9-%E5%8D%87%E7%BA%A7%E6%96%87%E4%BB%B6%E8%A7%A3%E6%9E%90.jpg)

## mbn格式简介
mbn是高通包含了特定运营商定制的一套efs，nv的集成包文件。同样的mbn文件会有很多。每个运营商都会有一个特定mbn包含在modem的代码中。需要使用高通最新的PDC工具load和激活，然后才能切换。
### 烧录mbn
烧录MBN文件：mbn文件是刷高通ril芯片的文件，需要用高通的QPST软件烧录，mbn直接是个文件，不需要解压，把QPST切换到software download—Multi-image，这个sheet就可以识别mbn文件烧录。具体如如下截图：
![QPST软件截图](https://github.com/gaozichen2012/linux-notes/blob/master/img/7-%E9%AB%98%E9%80%9A%E7%83%A7%E5%BD%95%E5%B7%A5%E5%85%B7.jpg)
## UBI格式简介
* UBI文件系统
* UBI全称Unsorted Block Imagine（未排序的块图像）
对flash设备来说它是一个卷管理系统，它管理着多个逻辑卷，而这些逻辑卷是基于一个单独的flash芯片的（逻辑块和物理块请参看），并且它可以将IO加载扩展至整块芯片（比如，损耗平衡）


## 为什么压缩包必须在普通用户环境下解压？
tar命令在解压时会默认指定参数--same-owner，即打包的时候是谁的，解压后就给谁；如果在解压时指定参数--no-same-owner（即tar --no-same-owner -zxvf xxxx.tar.gz），则会将执行该tar命令的用户作为解压后的文件目录的所有者。
![tar解压普通用户](https://github.com/gaozichen2012/linux-notes/blob/master/img/6-tar%E6%99%AE%E9%80%9A%E7%94%A8%E6%88%B7.jpg)

## 分析移远EC20 APP程序（KCM828机型）
### 创建线程 pthread_create
* Linux系统下的多线程遵循POSIX线程接口，称为pthread
* pthread_create()创建的线程并不具备与主线程（即调用pthread_create()的线程）同样的执行序列，而是使其运行start_routine(arg)函数。
```
int pthread_create(pthread_t *restrict tidp,
const pthread_attr_t *restrict attr,
void *(*start_rtn)(void), 
void *restrict arg);
Returns: 0 if OK, error number on failure
```
第一个参数tidp为指向线程标识符的指针。
第二个参数用来设置线程属性。（通常为NULL）
第三个参数是线程运行函数的起始地址。（通常为创建线程的函数名）
最后一个参数是运行函数的参数。（通常为NULL）

>C99 中新增加了`restrict`修饰的指针： 由`restrict`修饰的指针是最初唯一对指针所指向的对象进行存取的方法，仅当第二个指针基于第一个时，才能对对象进行存取。对对象的存取都限定于基于由`restrict`修饰的指针表达式中。 由`restrict`修饰的指针主要用于函数形参，或指向由`malloc()`分配的内存空间。restrict数据类型不改变程序的语义。 编译器能通过作出 restrict 修饰的指针是存取对象的唯一方法的假设，更好地优化某些类型的例程。

由于pthread库不是Linux系统默认的库，连接时需要使用库libpthread.a,所以在使用pthread_create创建线程时，在编译中要加-lpthread参数`gcc -o pthread -lpthread pthread.c`，-lpthread参数在KCM828机型的Makefile中的使用见下截图：
![-pthread](https://github.com/gaozichen2012/linux-notes/blob/master/img/10-lpthread.jpg)

#### 线程创建的Linux实现
Linux的线程实现是在核外进行的，核内提供的是创建进程的接口do_fork()。内核提供了两个系统调用__clone()和fork()，最终都用不同的参数调用do_fork()核内API。当然，要想实现线程，没有核心对多进程（其实是轻量级进程）共享数据段的支持是不行的，因 此，do_fork()提供了很多参数，包括CLONE_VM（共享内存空间）、CLONE_FS（共享文件系统信息）、CLONE_FILES（共享文 件描述符表）、CLONE_SIGHAND（共享信号句柄表）和CLONE_PID（共享进程ID，仅对核内进程，即0号进程有效）。当使用fork系统 调用时，内核调用do_fork()不使用任何共享属性，进程拥有独立的运行环境，而使用pthread_create()来创建线程时,则最终设置了所有这些属性来调用__clone()，而这些参数又全部传给核内的do_fork()，从而创建的"进程"拥有共享的运行环境，只有栈是独立的，由 __clone()传入。

Linux线程在核内是以轻量级进程的形式存在的，拥有独立的进程表项，而所有的创建、同步、删 除等操作都在核外pthread库中进行。pthread 库使用一个管理线程（__pthread_manager()，每个进程独立且唯一）来管理线程的创建和终止，为线程分配线程ID，发送线程相关的信号 （比如Cancel），而主线程（pthread_create()）的调用者则通过管道将请求信息传给管理线程。

# 如何自定义快捷指令
## 环境变量
Ubuntu Linux系统包含两类环境变量：
1. 系统环境变量和用户环境变量。系统环境变量对所有系统用户都有效
 系统环境变量相关文件：
* /etc/environment
* /etc/profile
* /etc/bash.bashrc
2. 用户环境变量仅仅对当前的用户有效
 用户环境变量相关文件：
* ~/.profile
* ~/.bash_profile 或者 ~./bash_login
* ~/.bashrc
## 利用环境变量来设置一些快捷命令
```
# 第一步：进入编辑环境变量文件
vi /etc/bash.bashrc

# 第二步：设置快捷指令，为需要打开的路径设置一个别名（快捷命令）
cp -r /mnt/hgfs/repository/TP38P-open /home/tom/ql-ol-sdk/open/

# 第三步：让修改立即生效（默认开机自启动）
source /etc/bash.bashrc
```
## Makefile相关知识点

## 分析KCM828机型的Makefile

# 问题点
* 根据移远OPEN快速入门手册，测试APP开发、Kernel开发、文件系统制作和usrdata.ubi制作（必须测试走一遍，完成以后再向黄工了解如何修改598的程序，编译、下载）
* 了解模块升级工具Quectel_Customer_FW_Download_Tool的升级文件类型
* 详细了解模块app开发内容，把入门手册中app开发再深入走一遍
