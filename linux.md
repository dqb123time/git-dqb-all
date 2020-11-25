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



# libvirt qemu kvm virsh

virsh的快照管理  https://blog.csdn.net/cpf945/article/details/101294849

[KVM之virsh管理Storage pool](https://www.cnblogs.com/wshenjin/p/11103945.html)

[virsh使用总结](https://www.cnblogs.com/wn1m/p/11280804.html)

## 存储池

https://www.cnblogs.com/kcxg/p/10689925.html

https://www.jianshu.com/p/397adc671e9c

https://blog.csdn.net/wylfengyujiancheng/article/details/102669460

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

## 快照

查看镜像信息  qemu info test.qcow2

为镜像创建快照  qemu-img snapshot -c snapshot01 test.qcow2 

列出某个镜像的所有快照 qemu-img snapshot -l test.qcow2 

使用快照 qemu-img snapshot -a snapshot01 test.qcow2 

删除快照 qemu-img snapshot -d snapshot01 test.qcow2

附：
 'snapshot' is the name of the snapshot to create, apply or delete
 '-a' applies a snapshot (revert disk to saved state)
 '-c' creates a snapshot
 '-d' deletes a snapshot
 '-l' lists all snapshots in the given image

kvm环境快照（snapshot）的使用方法 https://blog.csdn.net/gg296231363/article/details/6899533

**3-磁盘格式-快照和克隆 http://static.kancloud.cn/noahs/linux/1089716**

KVM虚拟机克隆方法总结（链接克隆） https://blog.csdn.net/wswit/article/details/52602207

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





# iSCSI详解 及 iSCSI配置

https://blog.csdn.net/tjiyu/article/details/52811458

## iscsiadm命令

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

# 环境变量

## linux

#### Linux的变量种类

按变量的生存周期来划分，Linux变量可分为两类： 
1 永久的：需要修改配置文件，变量永久生效。 
2 临时的：使用export命令声明即可，变量在关闭shell时失效。

#### 设置变量的三种方法

1 在/etc/profile文件中添加变量【对所有用户生效(永久的)】 
用VI在文件/etc/profile文件中增加变量，该变量将会对Linux下所有用户有效，并且是“永久的”。
例如：编辑/etc/profile文件，添加CLASSPATH变量 

```shell
# vi /etc/profile
export CLASSPATH=./JAVA_HOME/lib;$JAVA_HOME/jre/lib
```

注：修改文件后要想马上生效还要运行# source /etc/profile不然只能在下次重进此用户时生效。



2 在用户目录下的.bash_profile文件中增加变量【对单一用户生效(永久的)】 
用VI在用户目录下的.bash_profile文件中增加变量，改变量仅会对当前用户有效，并且是“永久的”。 
例如：编辑guok用户目录(/home/guok)下的.bash_profile 

```
$ vi /home/guok/.bash.profile 
```


添加如下内容： 

```
export CLASSPATH=./JAVA_HOME/lib;$JAVA_HOME/jre/lib 
```

注：修改文件后要想马上生效还要运行$ source /home/guok/.bash_profile不然只能在下次重进此用户时生效。



3 直接运行export命令定义变量【**只对当前shell(BASH)有效(临时的)**】 
在shell的命令行下直接使用[export 变量名=变量值] 定义变量，

该变量只在当前的shell(BASH)或其子shell(BASH)下是有效的，

shell关闭了，变量也就失效了，再打开新shell时就没有这个变量，需要使用的话还需要重新定义。

#### 环境变量的查看

1 使用echo命令查看单个环境变量。例如： 
echo $PATH 
2 使用env查看所有环境变量。例如： 
env 
3 使用set查看所有本地定义的环境变量。

#### 使用unset删除指定的环境变量

清除环境变量的值用unset命令。如果未指定值，则该变量值将被设为NULL。示例如下：

```
$ export TEST="Test..." #增加一个环境变量TEST
$ env|grep TEST #此命令有输入，证明环境变量TEST已经存在了
TEST=Test...
unset  TEST #删除环境变量TEST
$ env|grep TEST #此命令没有输出，证明环境变量TEST已经删除
```

#### 常用的环境变量

PATH 决定了shell将到哪些目录中寻找命令或程序 
HOME 当前用户主目录 
HISTSIZE　历史记录数 
LOGNAME 当前用户的登录名 
HOSTNAME　指主机的名称 
SHELL 当前用户Shell类型 
LANGUGE 　语言相关的环境变量，多语言可以修改此环境变量 
MAIL　当前用户的邮件存放目录 
PS1　基本提示符，对于root用户是#，对于普通用户是$

#### set env export的区别

每个shell都有自己特有的变量，这和用户变量是不同的。当前用户变量和你用什么shell无关，不管你用什么shell都是存在的。比如HOME,SHELL等这些变量，但shell自己的变量，不同的shell是不同的，比如BASH_ARGC， BASH等，这些变量只有set才会显示，是bash特有的。export不加参数的时候，显示哪些变量被导出成了用户变量，因为一个shell自己的变量可以通过export “导出”变成一个用户变量。

set 显示当前shell的变量，包括当前用户的变量 
env 显示当前用户的变量 
export 显示当前导出成用户变量的shell变量

 

使用export设置环境变量为导出，针对整个系统

使用env设置环境变量只设置一次

使用set设置环境变量等同于直接设置，如FOO=test

举个例子来讲:

```shell
[www.linuxidc.com@linuxidc ~]$ aaa=bbb --shell变量设定     
[www.linuxidc.com@linuxidc ~]$ echo $aaa      
bbb     
[www.linuxidc.com@linuxidc ~]$ env| grep aaa --设置完当前用户变量并没有     
[www.linuxidc.com@linuxidc ~]$ set| grep aaa  --shell变量有     
aaa=bbb     
[www.linuxidc.com@linuxidc ~]$ export| grep aaa --这个指的export也没导出，导出变量也没有     
[www.linuxidc.com@linuxidc ~]$ export aaa   --那么用export 导出一下     
[www.linuxidc.com@linuxidc ~]$ env| grep aaa  --发现用户变量内存在了     
aaa=bbb  
```

　　总结:linux 分 shell变量(set)，用户变量(env)， shell变量包含用户变量，export是一种命令工具，是显示那些通过export命令把shell变量中包含的用户变量导入给用户变量的那些变量。

## windows

1、**查看当前所有可用的环境变量**：输入 **set** 即可查看。

2、**查看某个环境变量**：输入 “**set 变量名**”即可，比如想查看path变量的值，即输入 set path

3、**修改环境变量** ：输入 “**set 变量名=变量内容****”即可，比如将path设置为“d:\hacker.exe”，只要输入set path="d:\nmake.exe"。注意，此修改环境变量是指用现在的内容去覆盖以前的内容，并不是追加。比如当我设置了上面的path路径之后，如果我再重新输入set path="c"，再次查看path路径的时候，其值为“c:”，而不是“d:\nmake.exe”;“c”。

4、**设置为空**：如果想将某一变量设置为空，**输入“set 变量名=”即可。如“set path=”** 那么查看path的时候就为空。注意，上面已经说了，只在当前命令行窗口起作用。因此查看path的时候不要去右击“我的电脑”——“属性”........

5、**给变量追加内容**（不同于3，那个是覆盖）：输入“**set 变量名=%变量名%;变量内容**”。如，为path添加一个新的路径，输入“ set path=%path%;d:\hacker.exe”即可将d:\hacker.exe添加到path中，再次执行"set path=%path%;c:"，那么，使用set path语句来查看的时候，将会有：d:\hacker.exe;c:，而不是像第3步中的只有c:。



```
%ALLUSERSPROFILE% 局部 返回所有“用户配置文件”的位置。
%APPDATA% 局部 返回默认情况下应用程序存储数据的位置。
%CD% 局部 返回当前目录字符串。
%CMDCMDLINE% 局部 返回用来启动当前的 Cmd.exe 的准确命令行。
%CMDEXTVERSION% 系统 返回当前的“命令处理程序扩展”的版本号。
%COMPUTERNAME% 系统 返回计算机的名称。
%COMSPEC% 系统 返回命令行解释器可执行程序的准确路径。
%DATE% 系统 返回当前日期。使用与 date /t 命令相同的格式。由 Cmd.exe 生成。有关 date 命令的详细信息，请参阅 Date。
%ERRORLEVEL% 系统 返回最近使用过的命令的错误代码。通常用非零值表示错误。
%HOMEDRIVE% 系统 返回连接到用户主目录的本地工作站驱动器号。基于主目录值的设置。用户主目录是在“本地用户和组”中指定的。
%HOMEPATH% 系统 返回用户主目录的完整路径。基于主目录值的设置。用户主目录是在“本地用户和组”中指定的。
%HOMESHARE% 系统 返回用户的共享主目录的网络路径。基于主目录值的设置。用户主目录是在“本地用户和组”中指定的。
%LOGONSEVER% 局部 返回验证当前登录会话的域控制器的名称。
%NUMBER_OF_PROCESSORS% 系统 指定安装在计算机上的处理器的数目。
%OS% 系统 返回操作系统的名称。Windows 2000 将操作系统显示为 Windows_NT。
%PATH% 系统 指定可执行文件的搜索路径。
%PATHEXT% 系统 返回操作系统认为可执行的文件扩展名的列表。
%PROCESSOR_ARCHITECTURE% 系统 返回处理器的芯片体系结构。值: x86，IA64。
%PROCESSOR_IDENTFIER% 系统 返回处理器说明。
%PROCESSOR_LEVEL% 系统 返回计算机上安装的处理器的型号。
%PROCESSOR_REVISION% 系统 返回处理器修订号的系统变量。
%PROMPT% 局部 返回当前解释程序的命令提示符设置。由 Cmd.exe 生成。
%RANDOM% 系统 返回 0 到 32767 之间的任意十进制数字。由 Cmd.exe 生成。
%SYSTEMDRIVE% 系统 返回包含 Windows XP 根目录（即系统根目录）的驱动器。
%SYSTEMROOT% 系统 返回 Windows XP 根目录的位置。
%TEMP% and %TMP% 系统和用户 返回对当前登录用户可用的应用程序所使用的默认临时目录。有些应用程序需要 TEMP，而其它应用程序则需要 TMP。
%TIME% 系统 返回当前时间。使用与 time /t 命令相同的格式。由 Cmd.exe 生成。有关 time 命令的详细信息，请参阅 Time。
%USERDOMAIN% 局部 返回包含用户帐户的域的名称。
%USERNAME% 局部 返回当前登录的用户的名称。
%UserProfile% 局部 返回当前用户的配置文件的位置。
%WINDIR% 系统 返回操作系统目录的位置。
```

## 通过代码获取环境变量

```java
package com.zken.test;
 
public class Test2 {
	public static void main(String[] args) {
		System.out.println(System.getenv());
		System.out.println(System.getProperties());
	}
}

getenv是获取系统的环境变更，对于windows在系统属性-->高级-->环境变量中设置的变量将显示在此(对于linux,通过export设置的变量将显示在此)

getProperties是获取系统的相关属性,包括文件编码,操作系统名称,区域,用户名等,此属性一般由jvm自动获取,不能设置. 
```



# [Linux set、env、declare、export显示shell变量的区别](https://www.cnblogs.com/wfwenchao/p/6139039.html)

https://www.cnblogs.com/wfwenchao/p/6139039.html

shell变量包括两种变量

### 1. shell局部变量

局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
通过赋值语句定义好的变量，可以通过如下方法定义shell变量

```
A1="1234"
delcare A2="2345"
```

### 2. 用户的环境变量

所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
通过export语法导出的shell私有变量，可以通过如下方法导出用户环境变量

```
A1="1234"
export A1  #先定义再导出
export A3="34"
```

### 显示shell变量

env 这是一个工具，或者说一个Linux命令，显示用户的环境变量。
set 显示用户的局部变量和用户环境变量。
export 显示导出成用户变量的shell局部变量，并显示变量的属性；就是显示由局部变量导出成环境变量的那些变量 （比如可以 export WWC导出一个环境变量，也可通过 declare -X LCY导出一个环境变量）
declare 跟set一样，显示用户的shell变量 （局部变量和环境变量）

### declare 命令

declare命令用于声明 shell 变量。
`declare WWC="wangwenchao"`
也可以省略declare
`WWC="wangwenchao"`

**用法**
declare [+/-][rxi][变量名称＝设置值] 或 declare -f

1. +/- 　"-"可用来指定变量的属性，"+"则是取消变量所设的属性。
2. -f 　仅显示函数。
3. r 　将变量设置为只读。
4. x 　指定的变量会成为环境变量，可供shell以外的程序来使用。
5. i 　[设置值]可以是数值，字符串或运算式。

### export 命令

export命令用于设置或显示环境变量。用于将局部变量导出成环境变量。

**用法**
export [-fnp][变量名称]=[变量设置值]

1. -f 　代表[变量名称]中为函数名称。
2. -n 　删除指定的变量。变量实际上并未删除，只是不会输出到后续指令的执行环境中。
3. -p 　列出所有的shell赋予程序的环境变量。

**PS: export命令的功能跟 declare功能部分重合；export WWC="wangwenchao" 相当于 declare -x WWC="wangwenchao"; 功能export -n WWC 相当于 declare +x WWC; 功能export查看导出的环境变量

### source 命令

source命令用法：
source FileName
作用:在当前bash环境下读取并执行FileName中的命令。
注：该命令通常用命令“.”来替代。
如：source .bash_rc 与 . .bash_rc 是等效的。

source命令与shell scripts的区别是，source在当前bash环境下执行命令，而scripts是启动一个子shell来执行命令。这样如果把设置环境变量（或alias等等）的命令写进scripts中，就只会影响子shell,无法改变当前的BASH,所以通过文件（命令列）设置环境变量时，要用source 命令。

source跟./xxx.sh或bash xxx.sh最大的不同就是前者在文件中设置的变量对当前shell是可见的，而后者设置的变量对当前shell是不可见的。要知道./xxx.sh和bash xxx.sh都是在当前shell的子shell中执行的，子shell中的变量不会影响父shell，而source是把文件中的命令都读出来一个个执行，所有的变量其实就是在父shell中设置的。

### nohup 命令

nohup 命令是 `no hanp up` 的缩写。
Unix/Linux下一般想让某个程序在后台运行，很多都是使用&在程序结尾来让程序自动运行；但如果要想在退出终端后，程序依然还在后台运行，则要用nohup与&组合来实现。

**`nohup`用法**
nohup Command [ Arg ... ] [&]

nohup 命令运行由 Command参数和任何相关的Arg参数指定的命令，同时忽略所有的挂起 (SIGHUP) 信号，或者修改用 -p 选项指定的进程来忽略所有的挂起 (SIGHUP) 信号(关闭终端、注销用户等，但是注意 ctrl + c 不是挂起信号)。
在注销后还可以使用 nohup 命令运行后台中的程序。要运行后台中的 nohup 命令，添加 &（表示“and”的符号）到命令的尾部。**输出将附加到当前目录的nohup.out 文件中**。

> 注： -p pid 和 Command 不能一起指定。
> 使用 -p pid 时，指定进程的输出将不会重定向到 nohup.out。
> `ctrl + c` `nohup bash wwc.sh` 会停止运行，但是 `bash wwc.sh &` 会正常运行；直接关闭终端 `nohup bash wwc.sh` 会正常运行，`bash wwc.sh &` 会停止；所以要 nohup 跟 & 结合使用。

**杀死由nohup执行的进程**

```
ps -ef | grep commond | grep -v grep  
例如：
ps -ef | grep "bash wwc.sh" | grep -v grep
```

找到进程id
kill -9 id

**参考链接：**https://www.ibm.com/support/knowledgecenter/zh/ssw_aix_72/com.ibm.aix.cmds4/nohup.htm

### type 命令

type命令用来显示指定命令的类型，判断给出的指令是内部指令还是外部指令。

**用法**

1. -t：输出“file”、“alias”或者“builtin”，分别表示给定的指令为“外部指令”、“命令别名”或者“内部指令”；
2. -p：如果给出的指令为外部指令，则显示其绝对路径；
3. -a：在环境变量“PATH”指定的路径中，显示给定指令的信息，包括命令别名。

**结果**
alias：别名。
keyword：关键字，Shell保留字。
function：函数，Shell函数。
builtin：内建命令，Shell内建命令。
file：文件，磁盘文件，外部命令。
unfound：没有找到。

### 内建命令和外部命令

**内建命令**
内部命令实际上是shell程序的一部分，其中包含的是一些**比较简单的linux系统命令**，这些命令由shell程序识别并在shell程序内部完成运行，通常在linux系统加载运行时shell就被加载并驻留在系统内存中。内部命令是写在bashy源码里面的，其执行速度比外部命令快，因为解析内部命令shell不需要创建子进程。比如：exit，history，cd，echo等。
有些命令是由于其**必要性**才内建的，例如cd用来改变目录，read会将来自用户（和文件）的输入数据传给Shell外亮。
另一种内建命令的存在则是为了**效率**，其中最典型的就是test命令，编写脚本时经常会用到它。另外还有I/O命令，例如echo于printf.

**外部命令**
外部命令是linux系统中的实用程序部分，因为实用程序的功能通常都比较强大，所以其包含的程序量也会很大，在系统加载时并不随系统一起被加载到内存中，而是在需要时才将其调用内存。通常外部命令的实体并不包含在shell中，但是其命令执行过程是由shell程序控制的。shell程序管理外部命令执行的路径查找、加载存放，并控制命令的执行。外部命令是在bash之外额外安装的，通常放在/bin，/usr/bin，/sbin，/usr/sbin......等等。可通过“echo $PATH”命令查看外部命令的存储路径，比如：ls、vi等。

外部命令就是由Shell副本（新的进程）所执行的命令，基本的过程如下：

1. 建立一个新的进程。此进程即为Shell的一个副本。
2. 在新的进程里，在PATH变量内所列出的目录中，寻找特定的命令。
   /bin:/usr/bin:/usr/X11R6/bin:/usr/local/bin为PATH变量典型的默认值。
   当命令名称包含有斜杠（/）符号时，将略过路径查找步骤。
3. 在新的进程里，以所找到的新程序取代执行中的Shell程序并执行。
4. 程序完成后，最初的Shell会接着从终端读取下一条命令，和执行脚本里的下一条命令。

**用户在命令行输入命令后，一般情况下Shell会fork并exec该命令，但是Shell的内建命令例外，执行内建命令相当于调用Shell进程中的一个函数，并不创建新的进程.**
**PS:为了实现强大的功能，有些内建命令也有了外部命令实现的版本，这些外部命令往往比对应的内部命令功能更强大，但是相对效率可能会低（例如：cd、echo等）；同样一些外部命令为了执行效力的问题，在内建命令中也有了实现（例如：printf）；如果一个命令既有内部实现又有外部实现，直接使用使用的是其内部版本，要想使用其外部实现，可以使用绝对路径，比如：echo是使用的其内部命令，/bin/echo是使用的其外部命令，查看一个外部命令的路径，可以使用 which 命令，例如`which echo`**

### 设置环境变量永久有效和临时有效

#### 永久有效

##### 对所有用户有效

/etc/profile 是每个用户登录时都会运行的环境变量设置文件，当用户第一次登录时,该文件被执行。对所有用户有效。
一个例子：

```
# System-wide .profile for sh(1)

if [ -x /usr/libexec/path_helper ]; then
    eval `/usr/libexec/path_helper -s`
fi

if [ "${BASH-no}" != "no" ]; then
    [ -r /etc/bashrc ] && . /etc/bashrc
fi

export THEOS=/opt/theos
export WWC=wangwenchao_lcy
```

##### 对当前用户有效

你可以把你要添加的环境变量添加到你主目录下面的.profile或者.bash_profile，如果该文件存在添加进去即可，如果该文件不存在，就在用户主目录下创建一个。

**总结：**

1. /etc/profile （建议不修改这个文件 ）
   全局（公有）配置，不管是哪个用户，登录时都会读取该文件。
2. /etc/bashrc （一般在这个文件中添加系统级环境变量）
   全局（公有）配置，bash shell执行时，不管是何种方式，都会读取此文件。可以设置系统提示符 PS1等。比如 PS1=\h:\W \u$，那么提示符的格式就是：主机:当前目录 用户名$
3. ~/.bash_profile 或者 ~/.profile （一般在这个文件中添加用户级环境变量）
   每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!

**PS: MAC下环境变量的加载顺序`/etc/profile /etc/paths ~/.bash_profile ~/.bash_login ~/.profile`，并且这几个文件`~/.bash_profile ~/.bash_login ~/.profile`是互斥的关系，如果前面的文件存在，后面的文件就不会加载**

### 其他配置文件

#### /etc/paths （全局建议修改这个文件）

编辑 paths，设置PATH环境变量

#### /etc/hosts

**主机名和IP配置文件**
hosts文件是Linux系统中一个负责IP地址与域名快速解析的文件，以ASCII格式保存在“/etc”目录下，文件名为“hosts”（不同的linux版本，这个配置文件也可能不同。比如Debian的对应文件是/etc/hostname）。hosts文件包含了IP地址和主机名之间的映射，还包括主机名的别名。在没有域名服务器的情况下，系统上的所有网络程序都通过查询该文件来解析对应于某个主机名的IP地址，否则就需要使用DNS服务程序来解决。通常可以将常用的域名和IP地址映射加入到hosts文件中，实现快速方便的访问。

hosts文件的格式如下：
IP地址 主机名/域名 主机别名(optional)

第一部份：网络IP地址；
第二部份：主机名或域名；
第三部份：主机名别名；

### 系统目录

#### usr目录

usr 是 unix system resources 的缩写

曾经的 /usr 还是用户的家目录，存放着各种用户文件 —— 现在已经被 /home 取代了（例如 /usr/someone 已经改为 /home/someone）。现代的 /usr 只专门存放各种程序和数据，用户目录已经转移。虽然 /usr 名称未改，不过其含义已经从“用户目录”变成了“unix 系统资源”目录。值得注意的是，在一些 unix 系统上，仍然把 /usr/someone 当做用户家目录，如 Minix。

#### /sbin、/bin、/usr/sbin、/usr/bin的区别

/sbin 主要放置一些系统管理的必备程式，如 reboot、shutdown等；/sbin 下的命令属于基本的系统命令

/bin 存放一些普通的基本命令，如ls,chmod等，这些命令在Linux系统里的配置文件脚本里经常用到

/usr/sbin 放置一些用户安装的系统管理程式，存放的一些非必须的系统命令

/usr/bin 存放一些用户命令

> 综述：如果这是用户和管理员必备的二进制文件，就会放在/bin。如果这是系统管理员必备，但是一般用户根本不会用到的二进制文件，就会放在 /sbin。相对而言。如果不是用户必备的二进制文件，多半会放在/usr/bin；如果不是系统管理员必备的工具，多半会放在/usr/sbin。

#### 从历史看Unix目录结构

话说1969年，Ken Thompson和Dennis Ritchie在小型机PDP-7上发明了Unix。1971年，他们将主机升级到了PDP-11。
没过多久，操作系统（根目录）变得越来越大，一块盘已经装不下了。于是，他们加上了第二盘RK05，并且规定第一块盘专门放系统程序，第二块盘专门放用户自己的程序，因此挂载的目录点取名为/usr。也就是说，根目录"/"挂载在第一块盘，"/usr"目录挂载在第二块盘。除此之外，两块盘的目录结构完全相同，第一块盘的目录（/bin, /sbin, /lib, /tmp...）都在/usr目录下重新出现一次。
后来，第二块盘也满了，他们只好又加了第三盘RK05，挂载的目录点取名为/home，并且规定/usr用于存放用户的程序，/home用于存放用户的数据。
从此，这种目录结构就延续了下来。随着硬盘容量越来越大，各个目录的含义进一步得到明确。

1. /：存放系统程序，也就是At&t开发的Unix程序。
2. /usr：存放Unix系统商（比如IBM和HP）开发的程序。
3. /usr/local：存放用户自己安装的程序。
4. /opt：在某些系统，用于存放第三方厂商开发的程序，所以取名为option，意为"选装"。

/usr/bin用于分发包管理器（如Ubuntu apt等）存放它所管理的应用的路径, /usr/sbin与/usr/bin的关系类似与/bin和/sbin的关系
/usr/local/bin用于存放**用户自己的程序（如自己编译出来的包等）**，不受分发包管理器的控制。如果用户把自己的程序放在/usr/bin下，则有可能在未来被包管理器给修改、删除或覆盖了同名文件。

通常/usr/bin下面的都是系统预装的可执行程序，会随着系统升级而改变
/usr/local/bin目录是给用户放置自己的可执行程序的地方，推荐放在这里，不会被系统升级而覆盖同名文件

**一般环境变量PATH都设置为 /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin，注意设置的顺序问题，如果在前面的目录找到了命令，就不会再搜索后边的目录。**

**参考链接：**[Unix目录结构的来历——阮一峰的网络日志](http://www.ruanyifeng.com/blog/2012/02/a_history_of_unix_directory_structure.html)

## 问题

Linux环境变量的加载顺序？



# linux统计文件行数

https://www.cnblogs.com/fullhouse/archive/2011/07/17/2108786.html

语法：wc [选项] 文件…

说明：该命令统计给定文件中的字节数、字数、行数。如果没有给出文件名，则从标准输入读取。wc同时也给出所有指定文件的总统计数。**字是由空格字符区分开的最大字符串**。

该命令各选项含义如下：

　　- c 统计字节数。

　　- l 统计行数。

　　- w 统计字数。

这些选项可以组合使用。

**输出列的顺序和数目不受选项的顺序和数目的影响。**

总是按下述顺序显示并且每项最多一列。

**行数、字数、字节数、文件名**

如果命令行中没有文件名，则输出中不出现文件名。

例如：

```shell
[root@cvknode ~]# wc -lwc test1 test2
 3  3  6 test1
 2  4  8 test2
 5  7 14 total
```

举例分析：

1.统计demo目录下，js文件数量：

find demo/ -name "*.js" |wc -l

2.统计demo目录下所有js文件代码行数：

find demo/ -name "*.js" |xargs cat|wc -l 或 wc -l `find ./ -name "*.js"`|tail -n1

3.统计demo目录下所有js文件代码行数，过滤了空行：

find /demo -name "*.js" |xargs cat|grep -v ^$|wc -l



# 设置http代理

## linux

export http_proxy=http://账号:密码@ip:port

export https_proxy=$http_proxy

export HTTP_PROXY=$http_proxy

export HTTPS_PROXY=$http_proxy

export all_proxy=$http_proxy 

export no_proxy="localhost,127.0.0.1" 

## windows

控制代理的环境变量分别是 http_proxy、http_proxy_user、http_proxy_pass，不区分大小写，分别代表代理地址（应是 http://ip:port 的形式）、代理用户名、代理密码，一般情况下只需要配置 http_proxy 即可（其余两个参数暂无条件测试，是否有作用未知），参数格式大致如下所示。

```
http_proxy=http://localhost:1080
http_proxy_user=zhangsan
http_proxy_pass=lisi
```

通过 `set` 命令的形式大致如下所示。

```shell
#设置参数
set http_proxy=http://localhost:1080
set http_proxy_user=zhangsan
set http_proxy_pass=lisi

#删除参数
set http_proxy=
set http_proxy_user=
set http_proxy_pass=
```

另外经测试还有 https_proxy 环境变量可配置，用于配置 https 的代理，如果未配置则将使用 http_proxy 的配置。据此可推测有 https_proxy_user 等参数。



set http_proxy=http://账号:密码@ip:port

set http_proxy_user=name

set http_proxy_pass=password

## git代理配置

查看http代理 git config --global http.proxy

取消http代理 git config --global --unset http.proxy

 配置http代理 git config --global http.proxy http://d19075:123456dqB-@devproxy.h3c.com:8080



据资料得，Linux 配置方式与 Windows 相似，仅命令及配置方式有所不同。

可配置的环境变量名分别为 http_proxy、https_proxy、ftp_proxy、no_proxy，分别是配置 http 代理、https 代理、ftp 代理、不使用代理的地址，参数格式大致如下所示（正确性有待考察，可能需要加 http:// 前缀），no_proxy 较特殊。

```shell
http_proxy=192.168.10.91:3128
https_proxy=192.168.10.91:3128
ftp_proxy=192.168.10.91:3128
no_proxy="127.0.0.1, localhost, 172.26.*, 172.25.6.66, 192.168.*"
```

