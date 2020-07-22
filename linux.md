# linux下查看uuid的三种方法及使用uuid的作用

https://blog.51cto.com/tiger2020/1535774

查看设备的uuid的三种方法，总结如下：

1 命令查看：blkid
2 文件查看：ls -l /dev/disk/by-uuid
3 命令查看：vol_id /dev/sda1

UUID的作用及意义

1：它是真正的唯一标志符

UUID为系统中的存储设备提供唯一的标识字符串，不管这个设备是什么类型的。如果你在系统中启动的时候，使用盘符挂载时，可能找不到设备而加载失败，而使用UUID挂载时，则不会有这样的问题。

2：设备名并非总是不变的

自动分配的设备名称并非总是一致的，它们依赖于启动时内核加载模块的顺序。如果你在插入了USB盘时启动了系统，而下次启动时又把它拔掉了，就有可能导致设备名分配不一致。

使用UUID对于挂载移动设备也非常有好处，它支持各种各样的卡，而使用UUID总可以使同一块卡挂载在同一个地方。
3：Ubuntu中的许多关键功能现在开始依赖于UUID

```
[root@cvknode1 ~]# ls -l /dev/disk/by-uuid/
total 0
lrwxrwxrwx 1 root root 10 Jul 20 18:46 2AD3-9321 -> ../../sda1
lrwxrwxrwx 1 root root 10 Jul 20 18:46 3231fb8d-e44b-4ef6-87b3-9e912a8386de -> ../../sda2
lrwxrwxrwx 1 root root  9 Jul 20 20:12 7d2ccb7e-e4a8-4550-9c91-58e720d27e3a -> ../../sdb
lrwxrwxrwx 1 root root 10 Jul 20 18:47 95edceef-9b89-4fad-8e49-65c86c0601a6 -> ../../sda5
lrwxrwxrwx 1 root root  9 Jul 20 20:20 97a5b074-1da9-41fc-8b92-2f5cc0fe3ac4 -> ../../sdf
lrwxrwxrwx 1 root root 10 Jul 20 18:46 a547768f-e83c-4cff-a2c8-da135cb535df -> ../../sda3
lrwxrwxrwx 1 root root 10 Jul 20 18:47 b3f9cca8-36f9-42fc-902a-8f24234f2750 -> ../../sda4


[root@cvknode1 ~]# blkid /dev/sdb
/dev/sdb: UUID="7d2ccb7e-e4a8-4550-9c91-58e720d27e3a" TYPE="ext4"

[root@cvknode1 ~]# blkid
/dev/sda2: UUID="3231fb8d-e44b-4ef6-87b3-9e912a8386de" TYPE="ext4" PARTUUID="4bab0750-dcdd-43bd-963f-4ff32ba30f55"
/dev/sda1: SEC_TYPE="msdos" UUID="2AD3-9321" TYPE="vfat" PARTLABEL="EFI System Partition" PARTUUID="bff508c2-d6dd-44bf-93ca-18f1e7821cf2"
/dev/sda3: UUID="a547768f-e83c-4cff-a2c8-da135cb535df" TYPE="swap" PARTUUID="d2e3d339-b05a-4bec-819c-fdb8d2c8d616"
/dev/sda4: UUID="b3f9cca8-36f9-42fc-902a-8f24234f2750" TYPE="ext4" PARTUUID="c07d8670-6504-42c0-b761-93d23daceea5"
/dev/sda5: UUID="95edceef-9b89-4fad-8e49-65c86c0601a6" TYPE="ext4" PARTUUID="2ef986ea-d1b7-489b-8b2b-8daa2fd05f89"
/dev/sdb: UUID="7d2ccb7e-e4a8-4550-9c91-58e720d27e3a" TYPE="ext4"
/dev/sdc: PTTYPE="gpt"
/dev/sde: PTTYPE="gpt"
/dev/sdd: PTTYPE="gpt"
/dev/sdf: UUID="97a5b074-1da9-41fc-8b92-2f5cc0fe3ac4" TYPE="ext4"
/dev/sdg: PTTYPE="gpt"

```



# libvirt

## 存储池

https://www.cnblogs.com/kcxg/p/10689925.html

https://www.jianshu.com/p/397adc671e9c

### 创建基于文件夹的存储池

```
#采用本地目录方式创建KVM存储池
[root@node72 ~]# mkdir -p /data/vmfs
#定义存储池
[root@node72 ~]# virsh pool-define-as vmfspool --type dir --target /data/vmfs
定义池 vmfspool
#创建存储池
[root@node72 ~]# virsh pool-build vmfspool
构建池 vmfspool
#查看所有存储池
[root@node72 ~]# virsh pool-list --all
 名称               状态     自动开始
-------------------------------------------
 vmfspool             不活跃  否       

[root@node72 ~]# virsh pool-info vmfspool
名称：       vmfspool
UUID:           c6d5bd62-3229-4a16-b267-081d943be80a
状态：       不活跃
持久：       是
自动启动： 否
#设置存储池自动启动
[root@node72 ~]# virsh pool-autostart vmfspool
池 vmfspool 标记为自动启动
#启动存储池
[root@node72 ~]# virsh pool-start vmfspool
池 vmfspool 已启动

[root@node72 ~]# virsh pool-list --all
 名称               状态     自动开始
-------------------------------------------
 vmfspool             活动     是       

[root@node72 ~]# virsh pool-info vmfspool
名称：       vmfspool
UUID:           c6d5bd62-3229-4a16-b267-081d943be80a
状态：       running
持久：       是
自动启动： 是
容量：       49.98 GiB     #显示挂载分区总容量
分配：       6.59 GiB      #分区已经使用容量
可用：       43.38 GiB     #可用容量
[root@node72 ~]# 
```

### 存储池的删除

```
#忘记记录了，
#virsh pool-destroy vmfspool
#virsh pool-undefine vmfspool
#virsh pool-delete vmfspool
```

