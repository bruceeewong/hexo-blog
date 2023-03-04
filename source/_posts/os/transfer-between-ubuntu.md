---
title: 记一次Ubuntu完美迁移系统盘的折腾
date: 2021-04-18 00:00:00
categories: OS
tags: [Linux]
---

> 平淡的下午写着代码，突然系统弹窗:"/目录空间不足!!!",一点 "检查"一看,原来还是自己刚学 linux 那会儿分配的 20G 可怜空间,而如今已经不想打开 windows 了,感叹到,该给 linux 涨涨地位了. 于是买来一张 250GB nvme SSD, 又不想重装系统, 想把系统盘一字节不落的 完美迁移 ,于是乎开始了迁移的折腾之旅~~~

## 环境说明

本人的环境信息如下:

- 原 250GB nvme SSD 装了双系统: 200G 给到 win10, 20G 给到 Ubuntu20
- 8Gx2 = 16G 内存
- Ubuntu 分区情况
  - /: 根目录 17G 主分区
  - /boot: 引导分区 1G 逻辑分区
  - swap: 交换分区 2G 逻辑分区
  - /home: 存储于机械硬盘,单独拆分出来: 100G

## 先结论后细节

整趟下来,踩了不少坑,终于把流程和思路理顺了! 期间查阅了很多以前不懂的 linux 知识,也算饶有收获~
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210418215518686.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)

## 折腾记录

### 购买 SSD,安装 SSD

> 此处知识点:
>
> 1. [ssd 固态硬盘 sata 的好还是 m.2 好呢？](https://www.zhihu.com/question/52811023/answer/606722409)
> 2. [一篇文章讲清什么是 NVMe](https://zhuanlan.zhihu.com/p/71932170)

如今 SSD 价格不算很贵,250GB 的西部数据 nvme 卡在某东上买花了 308+, 不得不说如今的硬件做的都很精致呀, m.2 接口的 SSD 小巧精致, 即插即用. 当然需要一颗专用螺丝固定在主板上 (螺丝是不随卡附赠的噢!) 一般主板支持几个 m.2 插槽就会配几个, 我的是 2 个插槽.

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021041821491745.png)

很快安装完毕,盖上机箱玻璃,嗯,有那种 强化+1 的满足感,哈哈哈.

### 制作 Ubuntu 启动 U 盘, 进入 live 环境

制作启动盘的原因是，我们需要一个能够操作硬盘的沙箱环境，相比 grub secure 那样纯命令行的环境，ubuntu 的 live 环境对于不那么熟练的人来说，还是很友好滴．

我是在 windows 环境下用 UltraISO 把下载的 ubuntu20 镜像文件写入 u 盘，这里直接看已有的教程吧：[Ubuntu 20.04 LTS 桌面版详细安装指南](https://www.sysgeek.cn/install-ubuntu-20-04-lts-desktop/)

### 格式化新硬盘的分区

这里有很多选择：

- fdisk
- parted
- Ubuntu 系统自带的 Disks 工具

在 linux 下给硬盘分区没搞过，于是了解一下 fdisk 和 parted，他们都是 linux 的磁盘管理命令，fdisk 只能操作 2TB 以下的分区，parted 是更新更强大的分区工具，支持 GPT 分区格式．

这里踩坑预警!! 如果想完美迁移系统盘，原来是什么分区类型，就按什么类型分．比如我原来是`msdos`分区，尝鲜使用`gpt`分区，在修复 grub 引导时就出错了．．．

第二个坑是手动设置分区时还会提示 4k 对齐警告（[知乎：4K 对齐，让你的 SSD 飞上天！
](https://zhuanlan.zhihu.com/p/71067178)），看起来硬盘存储不是从我们想象中位置 0 开始呢- .-

好吧，我不装了，老实用自带的 Disks 工具分区吧

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210418233916352.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)

> 分区方案参考文章
> [Ubuntu20.04 操作系统安装及重中之重：系统分区](https://zhuanlan.zhihu.com/p/268620595?utm_source=qq) > [Linux 主分区，扩展分区，逻辑分区的联系和区别](https://www.cnblogs.com/w-wfy/p/8870598.html) > [Linux / boot 分区的建议大小是多少？](https://qastack.cn/server/334663/what-is-the-recommended-size-for-a-linux-boot-partition)

经过思考，整张 250GB 卡都给到 ubuntu, 我的分区方案如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210418230043869.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)

### 硬盘根目录数据完整拷贝

> 参考文章：[迁移 linux 系统到新硬盘](https://zhuanlan.zhihu.com/p/33341983)

首先要知道: Linux 一切皆文件, 所以拷贝系统其实就是拷贝文件！

那这里使用`dd` 命令来进行字节级别的迁移，我的根目录所在的分区是/dev/nvme0n1p5，新硬盘划分的是/dev/nvme1n1p2。

```
dd if=/dev/nvme0n1p5 of=/dev/nvme1n1p2
```

由于 dd 命令没有展示中间过程，因此使用另一条命令来让他输出中间过程：

```
watch -n 5 killall -USR1 dd
```

17G 的数据在 nvme ssd 之间传输没有什么压力，很快传完

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210418230950964.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)

这里要注意，dd 命令也会拷贝 uuid 过去，意味着，新分区与旧分区的 uuid 是一样的。后面修改挂载信息会再提到．

拷贝完之后，还要更新一下分区信息，否则挂载后还是会显示原来的分区大小和使用情况：

```
umount /dev/nvme1n1p2
e2fsck -f /dev/nvme1n1p2
resize2fs /dev/nvme1n1p2
```

### BOOT 引导盘数据拷贝

同样 `/boot` 如果之前单独拆分出来，现在也要拷贝至新硬盘，但这里要避免 uuid 的重复，两个办法：

1. 使用 `dd` 复制，然后修改 UUID
2. 分区后自动产生新的 uuid，使用`cp`等文件拷贝方式拷贝引导盘数据

这里我选了后者．当然在 live 环境下，要学会自己用`mount`命令挂载盘符来操作噢．

### 更新新的根路径分区 uuid

> 参考文章：[Linux 修改分区 UUID](https://www.git2get.com/av/93492852.html)

由于不确定是否能成功，我想保留原有的盘符不动（方便回退），这样就会导致根目录的两个分区 uuid 相同，可能在引导时会错误，所以查阅资料，尝试改变新分区的 uuid．

其实也挺简单，使用系统自带的`uuidgen`命令产生 id, 然后作为参数更新新的分区，再写入新的根路径下`/etc/fstab`文件

```
uuidgen | xargs tune2fs /dev/nvme1n1p2 -U
```

执行命令查看最新盘符的 uuid

```
sudo blkid
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210418232411475.png)
复制最新根目录的 uuid，写入新根目录下的`/etc/fstab`，这里还是要先挂载盘符噢
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021041823264276.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
到这里，数据复制就完成了

### 修改 Grub 引导

直接推荐使用 `boot-repair` 工具，安装方式如下：

```
sudo add-apt-repository ppa:yannubuntu/boot-repair
sudo apt install -y boot-repair
```

安装完之后，运行命令会调起交互界面

```
boot-repair
```

这里注意要选择高级选项，手动指定新的引导盘，然后按提示修复引导．

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210418233323379.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
修复完毕，重启再进，系统完全没变，但是容量已经变为舒服的 184G 了！
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210418233431338.png)

### 后记

一趟踩坑下来又花费了我美好周日的一下午，写文章花了一晚上，但是谁叫我们是 coder 呢，学了好多硬盘相关的硬件和 linux 知识，最后升级成功，就是爱折腾~~

最后，装备强化完毕，又可以愉快地写代码了，冲！

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210418234448650.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
