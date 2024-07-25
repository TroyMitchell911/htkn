## 概要
- 板卡： lubancat-2n
- 主机环境：Ubuntu22.04
- 介绍了如何移植openEuler到lubancat-2n上

## 下载源码

openEuler的源码托管在gitee上而不在github上。

可以通过该地址获取到源码：https://gitee.com/openeuler/kernel.git。

本次移植对应上述仓库的OLK-6.6分支。

## 准备工作

经过检查对应的configs文件夹下并没有所需要的config文件。

通过检查euler官网发现支持了飞凌的ok3568板卡，与lubancat所使用的cpu一直，遂可以检查ok3568与lubancat-2n的硬件差异，进行修改。

### oebuild

oebuild是openEuler推出的镜像编译工具，基于yocto，非常方便，根据官方文档可以很轻松的搭建出oebuild环境。

官网地址：https://embedded.pages.openeuler.org/openEuler-23.03/yocto/oebuild.html#openeuler-embedded-oebuild

### 通过oebuild编译飞凌ok3568的镜像

通过oebuild可以很方便的几步就编译出ok3568的镜像，参考下面网址即可。

euler对于ok3568的文档地址：https://embedded.pages.openeuler.org/openEuler-23.03/bsp/arm64/rk3568/rk3568.html

