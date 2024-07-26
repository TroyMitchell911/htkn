## 概要
- 板卡： lubancat-2n
- 主机环境：Ubuntu22.04
- 介绍了如何移植openEuler到lubancat-2n上

## 下载源码

`openEuler`的源码托管在`gitee`上而不在`github`上。

可以通过该地址获取到源码：https://gitee.com/openeuler/kernel.git。

本次移植对应上述仓库的`OLK-6.6`分支。

## 准备工作

经过检查对应的`configs`文件夹下并没有所需要的`config`文件。

查看`euler`官网发现支持了飞凌的`ok3568`板卡，与`lubancat`所使用的`cpu`一致，所以我们可以先尝试是否能用这个镜像在`lubancat`上跑起来，如果可以，也就不用重复性做工作了。

### oebuild

`oebuild`是`openEuler`推出的镜像编译工具，基于`yocto`，非常方便，根据官方文档可以很轻松的搭建出`oebuild`环境。

官网地址：https://embedded.pages.openeuler.org/openEuler-23.03/yocto/oebuild.html#openeuler-embedded-oebuild

### 通过oebuild编译飞凌ok3568的镜像

通过`oebuild`可以很方便的几步就编译出`ok3568`的镜像，参考下面网址即可。

`euler`对于`ok3568`的文档地址：https://embedded.pages.openeuler.org/openEuler-23.03/bsp/arm64/rk3568/rk3568.html

编译成功后在`output`文件夹下可以看到一系列的镜像，不过经过尝试之后无法启动，原因是适配了飞凌的板子没有适配`lubancat`的板子，所以我们可以用的只有文件系统的镜像。

## 在OLK6.6分支做移植工作

由于`oebuild`出来的镜像我们不能用，那只能从`euler`官方维护的仓库分支下手了。

经过查看, 该版已经有对`rk3568`芯片的支持，但没有对`lubancat`板级的支持。

首先想到的是将`lubancat`的`dts`迁移到这里，但是`lubancat`的`dts`最新的也是`5.10`，与`6.6`差异较大，并且不确定`dts`背后对应的驱动是否还支持这些关键字，所以此时可以在`git`查找一下是否有人已经做过这些工作。

经过搜索在`gitee`上面发现了别人做过的`patch`：https://gitee.com/wang--ge/LubanCat_SDK_For_OpenEuler
[^1]: patch：基于已经发布的linux版本，别人做了修改以后可以生成patch补丁包，此时直接使用git am命令可以将补丁包应用到代码中，实现修改代码的目的。补丁包小巧方便快捷。

我们将`patch`一一打入源码中，发现已经有了`lubancat`的`defconfig`和`dts`文件，接下来就是制作分区表文件了。

参考`euler`官网所指导的`ok3568`的下载截图，可以很容易推测出`分区表文件`，分区表文件可以没有后缀或者是一个`txt`文件。

此时还缺一个`uboot`，我们首先想到的应该是拿`01章节`编译的`uboot`去测试一下能不能跑，如若不能，再去想适配一个新的`uboot`，毕竟不要重复造轮子。

经过测试，之前的`uboot`是可以用的，所以我们这里`uboot`就不用动了。

内核已经编译好了，我们接下来就是打包`dtb和image`为一个镜像了，野火的教程都是使用`sdk`的`build.sh`脚本进行编译打包的，那么我们可以分析这个`build.sh`文件自己编写一个打包脚本进行打包。

将镜像打包好之后，就可以进行下载了。

## 使用upgrade_tool下载

首先要将分区表文件先写入，否则不知道`uboot`，`boot`，`rootfs`应该存放到哪里。

接着依次将`boot.img`和`rootfs`写入，便可启动系统。

启动系统后应该能看到一个`OpenEuler`的`banner`，但是此时仅仅只是把`emmc`、`串口`、`时钟`等基础外设跑通，`pcie`、`sd卡`、`usb`、`i2c`等都还需要适配,本文所介绍的只是最基础的适配工作。
