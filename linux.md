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

# 修改hostname

https://blog.csdn.net/u013991521/article/details/80522269

1  修改配置文件：/etc/hostname 和 /etc/hosts 

2  hostnamectl 

```
sudo hostnamectl set-hostname <newhostname>

这条命令会删除/etc/hostname文件中的主机名，然后替换为新的主机名。和第一种方法一样，我们也需要更新/etc/hosts文件。这两种方法的本质都是一样的。
```

3   hostname

```
sudo hostname <new-hostname>

这条命令不会更改/etc/hostname文件中的静态主机名（static hostname），它更改的只是临时主机名（transient hostname）。所以重启计算机后会回到旧的主机名。

静态主机名保存在/etc/hostname文件中。
```





在CentOS中，有三种定义的主机名:静态的（static），瞬态的（transient），和灵活的（pretty）

https://blog.csdn.net/tantexian/article/details/45958275

```
“静态”主机名也称为内核主机名，是系统在启动时从/etc/hostname自动初始化的主机名。“瞬态”主机名是在系统运行时临时分配的主机名，例如，通过DHCP或mDNS服务器分配。静态主机名和瞬态主机名都遵从作为互联网域名同样的字符限制规则。而另一方面，“灵活”主机名则允许使用自由形式（包括特殊/空白字符）的主机名，以展示给终端用户（如Linuxidc）。
```

要查看主机名相关的设置

```
hostnamectl  
或hostnamectl status
```

只查看静态、瞬态或灵活主机名，分别使用“--static”，“--transient”或“--pretty”选项。

```
[root@localhost ~]# hostnamectl --static
localhost.localdomain
[root@localhost ~]# hostnamectl --transient
localhost.localdomain
[root@localhost ~]# hostnamectl --pretty
```

3.要同时修改所有三个主机名：静态、瞬态和灵活主机名：

```
hostnamectl set-hostname Linuxidc
```

# 端口

## linux

查看端口占用情况

```
lsof -i tcp:80
lsof -i :80
```

```
netstat -ntlp 列出所有端口
netstat -nap #会列出所有正在使用的端口及关联的进程/应用
lsof -i :portnumber #portnumber要用具体的端口号代替，可以直接列出该端口听使用进程/应用
netstat -lnp|grep 88   #88请换为你的apache需要的端口，如：80  (检查端口被哪个进程占用)

ps 1777  查看进程的详细信息 （或 ps -ef |grep 1777）
kill -9 1777        #杀掉编号为1777的进程（请根据实际情况输入）  
```

开启或关闭端口：

https://www.centos.bz/2018/03/centos%E6%9F%A5%E7%9C%8B%E7%AB%AF%E5%8F%A3%E5%8D%A0%E7%94%A8%E6%83%85%E5%86%B5%E5%92%8C%E5%BC%80%E5%90%AF%E7%AB%AF%E5%8F%A3%E5%91%BD%E4%BB%A4/

## windows

```
netstat -nao #会列出端口关联的的进程号，可以通过任务管理器查看是哪个任务

最后一列为程序PID，再通过tasklist命令：tasklist | findstr 2724
再通过任务管理结束掉这个程序就可以了
```



# 磁盘分区/格式化

原文链接：https://blog.csdn.net/u010098331/java/article/details/51473191

LINUX下新增的磁盘不建立分区，直接建立文件系统并挂载：
 不是都要先使用FDISK进行分区的么？怎么直接跳过了这步，直接建立文件系统，并挂载了呢？

解决方法：

```
假设新硬盘是 /dev/sdc
fdisk操作的是/dev/sdc ，分区后才会有/dev/sdc1 /dev/sdc2 之类
一般mkfs.ext4 /dev/sdc1 来格式化一个分区，再mount /dev/sdc1
不过你也可以不分区，直接mkfs.ext4 /dev/sdc ，然后mount /dev/sdc
```

原理：

这跟农民伯伯种地是一个道理。
把一亩地分成十个畦是为了浇水的时候方便，但是这不意味这不分畦就不能种庄稼

# mount/umount 和 /etc/fstab

https://blog.51cto.com/zkxfoo/1758529

https://www.cnblogs.com/arnoldlu/p/11613842.html  [fstab是什么？被谁用？怎么写？](https://www.cnblogs.com/arnoldlu/p/11613842.html)

https://blog.51cto.com/lspgyy/1297432  /etc/fstab文件 详解

挂载：mount

卸载：umount

自动挂载配置文件：/etc/fstab

## mount

手动挂载设备
mount 挂载命令

```
格式： mount [options] [-t fstype] [-o option] 设备 挂载点
常用选项 [ options ]：

常用选项：
     -t fstype(ext2、ext3、ext4、xfs、iso9660、smb等)
     -r: 只读挂载
     -w: 读写
     -L lable: 以卷标指定， LABLE=“label”
     -U UUID：以UUID指定挂载设备，UUID=“UUID”
     -a: 自动挂载所有（/etc/fstab文件中）支持自动挂载的设备

     --bind Dir1 Dir2 己经挂载了的文件，可以再次绑定其它目录上使用

     -n: 不更新/etc/mtab文件

-o options
        async: 异步I/O
        sync: 同步I/O
        noatime/atime: 建议noatime
        auto/noauto: 是否能够被mount -a选项自动挂载；
        diratime/nodiratime: 是否更新目录的访问时间戳；
        exec/noexec：是否允许执行其中的二进制程序；
        _netdev: 网络设备
        remount: 重新挂载
        acl: 启用facl

            # tune2fs -o mount-option 设备
            # tune2fs -o ^mount-option 取消
```

示例：挂载，默认不指定选项会自动附加如下属性

defaults（ rw, suid, dev, exec, auto, nouser, async, and relatime.）

```
[root@zibbix ~]# mount /dev/sda5 /mnt/sda5
```

查看挂载情况: mount、 lsblk 、df -h

```
[root@zibbix ~]# mount | grep "sda5"
/dev/sda5 on /mnt/sda5 type ext4 (rw)
```

查看占用挂载设备的进程 ： fuser -v 挂载点

杀死挂载进程：  fuser -km 挂载点

## umount

当卸载一个文件系统时，如果出现如下信息，表示该设备有人正在使用。

```
[root@zibbix ~]# umount /dev/sda5
umount: /mnt/sda5: device is busy.
        (In some cases useful info about processes that use
         the device is found by lsof(8) or fuser(1))
```

先查看

```
[root@zibbix ~]# fuser -c /mnt/sda5
/mnt/sda5:           13796c
[root@zibbix ~]# fuser -c /mnt/sda5/find/
/mnt/sda5/find/:     13796c
```

杀死正在使用该文件系统的用户，然后才能进行卸载

```
[root@zibbix ~]# fuser -km /mnt/sda5
/mnt/sda5:           13796c
```

卸载

```
# umount 挂载设备|持载点
```

## fstab

```
[root@zibbix ~]# cat /etc/fstab
#
# /etc/fstab
# Created by anaconda on Fri Jul 31 23:50:21 2015
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/vg_system-root /                       ext4    defaults        1 1
UUID=3a9c20f4-0cc2-4563-9e2c-d4833c1463c2 /boot                   ext4    defaults        1 2
/dev/mapper/vg_system-var /var                    ext4    defaults        1 2
/dev/mapper/vg_system-swap swap                    swap    defaults        0 0
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0

[root@cvknode4 ~]# cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Wed Jul 22 18:06:54 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=eed4092c-ff0d-44f1-b47c-89d96b943734 /                       ext4    defaults        1 1
UUID=FBCD-CDF8          /boot/efi               vfat    defaults,uid=0,gid=0,umask=0077,shortname=winnt 0 0
UUID=1fada164-7025-4c9c-a87b-d2033f7229ac /var/log                ext4    defaults        1 2
UUID=76df6860-dbbf-47ca-9a84-632c0057799d /vms                    ext4    defaults        1 2
UUID=6d637cb6-1fd6-4cb5-9ead-bfaee5a85327 swap                    swap    defaults        0 0
UUID=212e8134-730c-44d0-8583-dd7c3151cd5a /vms/uiscloud ext4 defaults 0 2

```



```
该文件分为六段各段的意义如下：
 设备     挂载点   文件系统类型  挂载选项   转储频度   自检次序
 
（1）要挂载的设备：
        设备文件、LABEL=, UUID=
（2）挂载点：
        swap没有挂载点，挂载点为swap
（3）文件系统类型
        ext2、ext3、ext4、xfs、nfs、smb、iso9660等
（4）挂载选项：多个选项间使用逗号分隔;
        async、sync、_netdev
        defaults（ rw,  suid, dev, exec, auto, nouser, async, and relatime.）
（5）转储频率：
         0：从不备份
         1：每日备份
         2：每隔一天备份
（6）自检次序：
         0: 不自检
         1：首先自检，通常只能被/使用；
         2：等数字为1的自检完成后，再进行自检
```



注意：配置完该文件不会立即生效，可以重启操作系统或使用mount -a来使该文件立即生效

若mount -a未生效，尝试systemctl daemon-reload;mount -a 



# iscsiadm命令

https://blog.51cto.com/xiaocao13140/1931854

```
1.发现iscsi存储: iscsiadm -m discovery -t st -p ISCSI_IP
2.查看iscsi发现记录 iscsiadm -m node
3.删除iscsi发现记录 iscsiadm -m node -o delete -T IQN -p ISCSI_IP
4.登录iscsi存储 iscsiadm -m node -T IQN -p ISCSI_IP -l
5.登出iscsi存储 iscsiadm -m node -T IQN -p ISCSI_IP -u
6 显示会话情况  iscsiadm -m session

注：-T -p可以只有一个

iscsiadm -m discovery -t sendtargets -p 192.168.1.1
iscsiadm -m node
iscsiadm -m node -L all  登录所有
iscsiadm -m node -T iqn.2001-04.com.example-test -p 192.168.1.1 --login
iscsiadm -m node -U all  登出所有
iscsiadm -m node -T iqn.2001-04.com.example-test -p 192.168.1.1 --logout

注销/登出target：
iscsiadm -m node -T iqn.2011.08.accusys.com:21dcc2b22211a191:ist0 --logout

删除target：
iscsiadm -m node -o delete -T iqn.2011.08.accusys.com:21dcc2b22211a191:ist0

注销到所有targets的连接
iscsiadm -m node --logoutall=all

显示数据库中discovery记录
[root@cvknode4 uis-core]# iscsiadm -m discovery
10.10.9.10:3260 via sendtargets
10.10.9.1:3260 via sendtargets

显示数据库中的所有的节点
[root@cvknode4 uis-core]# iscsiadm -m node
10.10.9.1:3260,1 iqn.2018-01.com.h3c.onestor:6e79d4cdc4d04fe2b964706654234ba3

显示所有的会话和连接
[root@cvknode4 uis-core]# iscsiadm -m session
tcp: [59] 10.10.9.1:3260,1 iqn.2018-01.com.h3c.onestor:6e79d4cdc4d04fe2b964706654234ba3 (non-flash)

```

