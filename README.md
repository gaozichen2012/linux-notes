# linux-notes
此笔记存放移远模块调试记录，linux相关笔记暂存都存在README中，后续内容增多，在区分子笔记本，再在README中汇总

## 相关概念及注意事项
* 移远SDK 压缩包的解压必须在 Ubuntu“普通用户”环境下解压。
* QuecOpenTM是一种以模组作为主处理器的应用方式。采用QuecOpenTM解决方案，可以简化用户对无线应用的开发流程，精简硬件结构设计，从而降低产品成本。
* 所有开发之前，都必须先执行`source ql-ol-crosstool/ql-ol-crosstool-env-init`
* 移远SDK不能放在`VMTOOLS`目录下，不然make的时候会出现错误

## 交叉编译生成可执行文件并下载到机器执行的步骤
> 移远SDK以普通用户解压是因为tar命令的要求，以root解压所有权归root，以普通用户解压所有权归普通用户
1. 以普通用户`tom`解压移远SDK `tar -jxvf EC20CEHCLGR06A05V03M1G_OCPU_RC.tar.bz2`
2. 初始化交叉编译环境 `source ql-ol-crosstool/ql-ol-crosstool-env-init`
![初始化交叉编译环境](https://github.com/gaozichen2012/linux-notes/blob/master/img/5-1-APP%E5%BC%80%E5%8F%91.jpg)
3. `make`生辰可执行文件，并传给VMTools
![make](https://github.com/gaozichen2012/linux-notes/blob/master/img/5-2-APP%E5%BC%80%E5%8F%91.jpg)
4. 将可执行文件通过adb传到`/usrdata`目录下，修改可执行文件的权限，并进入adb shell执行该文件
![执行](https://github.com/gaozichen2012/linux-notes/blob/master/img/5-3-APP%E5%BC%80%E5%8F%91.jpg)

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
## 为什么压缩包必须在普通用户环境下解压？
tar命令在解压时会默认指定参数--same-owner，即打包的时候是谁的，解压后就给谁；如果在解压时指定参数--no-same-owner（即tar --no-same-owner -zxvf xxxx.tar.gz），则会将执行该tar命令的用户作为解压后的文件目录的所有者。
![tar解压普通用户](https://github.com/gaozichen2012/linux-notes/blob/master/img/6-tar%E6%99%AE%E9%80%9A%E7%94%A8%E6%88%B7.jpg)
# 问题点
* EC20CEHCLGR06A05V03M1G_OCPU_RC.tar.bz2是SDK，EC20CEHCLGR06A05V03M1G_TP598.rar是什么？能不能在windows下解压？
* 为什么在VMTOOLS下编译不行，在ubuntu目录下就可以
