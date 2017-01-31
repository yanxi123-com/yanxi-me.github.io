---
layout: post
title: 图书城整体迁移至阿里云完毕
category: 创业
---

经过一个多星期的调试，图书城网站及所有后台程序这周已经完全迁移到阿里云上去了。 之前的云服务器60G硬盘不出意料的在迁移2天后就满了，所以刚又增加了200G磁盘空间，和第一块数据磁盘一样，仍需要另行挂载。

至此，所用阿里云服务总览如下：

* 云服务器：2核处理器 1.5G内存 带宽5Mbps 总磁盘260GB；4540元/年</li>
* 关系型数据库RDS：MySql新3型，1200M内存，50G空间；5100元/年</li>
* 开放存储服务OSS：按需使用，目前消耗空间133G；预估2000元/年</li>

总价格超过1.1万每年，比我之前预计的要贵不少。主要因为之前托管一台1U的双核服务器就能搞定web+数据库+存储，而在云上拆成了3块（阿里云磁盘IO性能差无法自行安装数据库，存储用OSS也有待商榷）。其中MySql价格偏贵，本来新2型完全能够满足需求，就是空间略小，要是也能像云服务器那样可以自由配置就好了。就这两天来看，网站访问速度和性能有所提升，但还需进一步观察。

<!-- more -->

附：**阿里云服务器硬盘挂载** 参考：

[http://www.netingcn.com/disk-mount.html](http://www.netingcn.com/disk-mount.html)


阿里云提供云服务器的硬盘由两块组成，一块是系统盘，一块是数据盘，默认数据盘是没有挂载的，如果要用到数据盘就需要自己手动挂载。通过命令fdisk -l，可以看到设备名为/dev/xvdb。另外在挂载前要对硬盘（/dev/xvdb）分区和格式化。分区使用命令fdisk，格式化命令为mkfs.ext3。

    fdisk /dev/xvdb
    
    （输入n）
    Command (m for help): n
    
    Command action
    e extended
    p primary partition (1-4)
    （输入p）
    p
    
    （输入1）
    Partition number (1-4): 1
    
    （输入p）
    Command (m for help): p
    
    （输入w）
    Command (m for help): w

此时在使用`fdisk -l`查看应该可以看到类似下面的结果

    Disk /dev/xvdb: 246.9 GB, 246960619520 bytes
    255 heads, 63 sectors/track, 30024 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    
    Device Boot Start End Blocks Id System
    /dev/xvdb1 1 30024 241167748+ 83 Linux
    
    上述执行完分区了，最好在执行格式化前重启一下机器。
    
    格式化分区
    mkfs.ext3 /dev/xvdb1
    
    格式化根据硬盘的大小不同时间也不同，就等待吧。
    
    假设把此硬盘挂在在 /data目录下，先创建data
    mkdir -pv /data
    
    挂载硬盘
    mount /dev/xvdb1 /data

写入分区表

    echo "/dev/xvdb1 /data ext3 defaults 1 1" >> /etc/fstab

也可以直接写入分区表后使用 mount -a 来使之生效。

