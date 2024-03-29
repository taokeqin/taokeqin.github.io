Title: 我的备份方案
Date: 2023-03-18 21:16
Modified: 2023-03-18 21:16
Category: misc
Tags: 备份, NAS, WeiXin
Slug: my-backup-solution
Authors: Hacken
Summary: 备份的选项。

越来越多的信息需要备份以防在极端情况的数据丢失。比如手机掉了，电脑坏了，账号丢了。

最好先思考，并遵守的好规则：

1. 善用各个设备自带的备份/迁移/恢复功能
2. 善用各个软件的备份/迁移/恢复功能

## 华为手机

所以我要用华为手机的备份功能，但是图片视频的备份我直接备份 DCMI 目录，这样我就可以用 rsync 增量备份。
需要增量的原因很简单，手机上的有很多视频都是可以删除的

```bash
rsync -rat --ignore-existing --progress DCIM/ /target/
```

这里面有个问题是 DCMI 的数据只能通过 hisuite 导出，而导出后文件的创建个修改时间都不能保持以前的。
TODO: 测试通过 hisute 的备份功能备份图片视频，看看能不能直接访问 DCMI 简洁的用 rsync 备份

## 微信

在微信的设置-》聊天选项里面有备份和迁移选项，但是都要通过 PC 端软件来进行，这样备份的一个好处是可以恢复。
对于一些已经封存的聊天，这样挺好的，比如小孩自幼儿园的班级群。
但是增量备份怎么做呢？比如小孩目前的班级群，备份后我希望清理一下手机。

前一次备份的时候我有文件：1，2，3
后一次我做了清理，删除了 1，2， 然后增加了 4，5
我希望备份能让最终结果变成：1，2，3，4，5
而不是用最新的 3，4，5 来覆盖。

测试的结果是微信的备份是合并备份，在手机上删除的不会删除备份里面的，而且之前备份了然后从手机上删除了的也可以从备份里面恢复回来。
唯一的缺点是备份不是可读的，文件格式是微信内部定义的。
