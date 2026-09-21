# 一、IP地址
## 1.1ip地址的定义
- ip地址是指互联网上的地址，在国际互联网中，这个IP地址是唯一的
- ip地址由32位二进制数组成，分为四段，为了方便人类阅读，通常用四个十进制数表示
```
   192    .       168  .   1            .    100
11000000  .   10101000 .  00000001     .   01100100

IP地址最小值
00000000  .   00000000 .  00000000     .   00000000   #二进制
0.0.0.0   #十进制

IP地址最大值
11111111  .   11111111 .  11111111     .  11111111   #二进制
255       .     255    .        255     .    255

192.168.256.1
192.168.1.256
```
## 1.2、IP地址分类
### 1.2.1、按协议分
- ipv4:  32位**二进制数**组成，总计2^32次-1，约43亿左右。分四4组（段），组与组之间用点号进行分隔。
- ipv6：互联网协议第六版，想用来替代IPV4的。IPV6地址长度是128位，IPV6是划分为8组，每4个十六进制为一组，组与组之间用冒号进行分隔。总计IP数约340万亿个。
```
2001：fe21:9eff:4343:0000:0000:0000:0000
缩写为
2001：fe21:9eff:4343:：
```
IPV6没有广泛应用的原因
```
1、IP地址难记；2、NAT技术广泛应用；3、升级设备，费用高
```
### 1.2.2、按范围分
IP地址=网络地址+主机地址
```
IP地址：192  . 168  .  1   .  100   
子网掩码：255.255.255.0
网络地址：192.168.1.x
```

A类第1位以0开头

```
0xxxxxxx  .   xxxxxxx  .  xxxxxxx      .   xxxxxxx 
```

B类地址，以10开头的

```
10xxxxxx  .   xxxxxxx  .  xxxxxxx      .   xxxxxxx 
```

C类地址，以110开头的

```
110xxxxx  .   xxxxxxx  .  xxxxxxx      .   xxxxxxx 
```

D类地址，以1110开头的

```
1110xxxx  .   xxxxxxx  .  xxxxxxx      .   xxxxxxx 
```

E类地址，以11110开头的

```
11110xxx  .   xxxxxxx  .  xxxxxxx      .   xxxxxxx 
```

![image-20260127195506502](https://gk-1251416864.cos.ap-guangzhou.myqcloud.com/md/20260127195513632.png?imageSlim)

### 1.2.3按用途分
公有IP：需要向管理机构申请，花钱买
私有IP：随意用，不花钱
保留IP：特殊用途

![image-20260127200047543](https://gk-1251416864.cos.ap-guangzhou.myqcloud.com/md/20260127200047598.png?imageSlim)

# 二、子网掩码

子网掩码用来表示一个IP地址中，哪些是网络段，哪些是主机号
用全1表示网络号，全0表示主机号

```
IP地址：192  . 168  .  1   .  100   
子网掩码：255.255.255.0（11111111.11111111.11111111.00000000）
网络地址：192.168.1
主机地址：100
```

子网掩码同IP地址一样，也是32位二进制数组成
子网掩码与IP地址配套使用，通过它们进行逻辑与运算，可以判断两个IP地址，是不是在同一个网络。

```
子网掩码：255.255.255.0
```


```
10.10.10.1/255.0.0.0  =10.x.x.x
10.10.0.1/255.0.0.0  = 10.x.x.x
10.0.0.1/255.0.0.0 = 10.x.x.x
在同一个网段里
```

子网掩码缩写表示

### 子网划分

|**CIDR**|**子网掩码**|可用IP地址|
|---|---|---|
|/8|255.0.0.0|16,777,214（1千6百万)|
|/16|255.255.0.0|65,534|
|/22|255.255.252.0|1,022|
|/23|255.255.254.0|510|
|/24|255.255.255.0|254|
|/25|255.255.255.128|126|
|/30|255.255.255.252|2|
|/32|255.255.255.255|1|

默认IPV4地址，只分A类,B类,C类，这样的划分会造成大量的浪费或者不够用，为解决IPV4的不足，提高网络划分的灵活性，我们有必要对网络进行划分，把一个大的网络划分成多个小的子网，应用的技术是VLSM，而在路由层进行子网的聚合，则是应用技术CIDR。

划分思想：借用现有的网段的主机位最左边某几位作为子网位，划分出多个子网。 1、把原来有类网络中的“网络ID”部分向“主机ID”部分借位 2、把一些属于“主机ID”部分的位变成“网络ID”的一部分，有人叫它“子网ID” 3、新的“网络ID”就是原来的“网络ID”+“子网ID”，“子网ID”的长度决定了划分的子网的数量

```
IP地址：192  . 168  .  1   .  100   
子网掩码：255.255.255.0（11111111.11111111.11111111.00000000）
网络地址：192.168.1
主机地址：100
```

![image20260127201729010](https://gk-1251416864.cos.ap-guangzhou.myqcloud.com/md/20260127201729083.png?imageSlim)



```
192.168.1.0 255.255.255.0
拆分为2个网络
192.168.1.0  255.255.255.128
192.168.1.128 255.255.255.128
```

# 三、网关

gateway又叫网间连接器、协议转换器，默认网关在网络层实现网络互联。

网关即可用于广域网互连，也可以用于局域网互连。
在局域网中，网关实质上是一个网络通向其他网络的IP地址。
![image-20260127202623069](https://gk-1251416864.cos.ap-guangzhou.myqcloud.com/md/20260127202623148.png?imageSlim)

在实际网络中，网关一般是开始第1个，或者最后最大的那个

```
192.168.1.1
192.168.1.2    #也有这个场景，网络里高可用设置
192.168.1.100  #不适合当网关
192.168.1.254
```

vmwarm虚拟网卡的网关是x.x.x.2！！！

# 四、MAC地址

MAC地址的全称是Media Access Control，意为媒体访问控制，
也被称为是计算机的物理地址或硬件地址，它一般由厂家直接烧录在网卡设备上，它具有唯一性，可修改。

MAC地址由48位二进制数组成，为了方便表示，采用十六进制，总计12个十六进制数，每2个为一组，整体表现形式为01-28-EB-AE-4C-52。

由于计算机约定8比特为1个字节，所以MAC地址长度一共是六个字节。

为避免MAC地址重复，确保唯一性，前三个字节(01-28-eb)是由IEEE的注册管理机构RA机型分配给网卡生产商（苹果公司：`00:1C:B3` 思科公司：`00:1E:58` 华为公司：`00:1E:10`），后三个字节则是由各个生产商自行指派，唯一设备标识符。

MAC地址作用
- **唯一性**：每个设备的MAC地址都是全球唯一的，由设备制造商根据IEEE（电气和电子工程师协会）分配的地址空间生成。
    
- **硬件层通信**：MAC地址用于局域网（LAN）中设备之间的直接通信。例如，当你通过局域网发送数据包时，数据包会使用目标设备的MAC地址来确定目标。
    
- **局限性**：
    
- **只在同一网络中有效**：MAC地址是硬件地址，它只能在同一个局域网（LAN）内有效，不能跨网络（比如不同的子网或互联网）使用。
    
- **不具备路由功能**：路由器无法通过MAC地址进行跨网络路由，因为它们无法识别不同子网中的MAC地址。
    
- **MAC地址的随机化**为了保护隐私，某些设备和操作系统（如手机、笔记本电脑）会在使用Wi-Fi和蓝牙时启用**MAC地址随机化**功能。这意味着设备在连接到Wi-Fi或蓝牙时，会使用临时生成的“虚拟”MAC地址，而不是设备的真实MAC地址，以避免被追踪。这个临时MAC地址是随机生成的，并且会定期改变。


# 五、网卡常用 配置参数

![image20260127203800623](https://gk-1251416864.cos.ap-guangzhou.myqcloud.com/md/20260127203800685.png?imageSlim)

![image20260127204350304](https://gk-1251416864.cos.ap-guangzhou.myqcloud.com/md/20260127204350368.png?imageSlim)

## 六、IP地址配置与分配

### 6.1、静态与动态

- **静态IP地址配置**：
    
- 适用于设备固定连接互联网，通常用于服务器、打印机等设备。
    
- 配置方法：手动设置IP地址、子网掩码和网关。
    
- **动态IP地址配置**：
    
- 使用DHCP（动态主机配置协议）自动分配IP地址。
    
- 优点：简化网络配置，避免IP地址冲突。
    
- **查看本机IP地址**：
    
- Linux命令：`ifconfig` 或 `ip a`
    
- Windows命令：`ipconfig`
    

### 6.2、配置Linux系统中的IP地址

三系统网络配置对比总览

|配置项|CentOS 7.9|Ubuntu (22.04+)|Rocky Linux 9|
|---|---|---|---|
|**主要配置文件**|`/etc/sysconfig/network-scripts/ifcfg-xxx`|`/etc/netplan/*.yaml`|NetworkManager + keyfile格式|
|**默认网络管理器**|NetworkManager（建议）或 network|systemd-networkd + NetworkManager|NetworkManager（默认且唯一）|
|**配置文件语法**|KEY=VALUE 格式|YAML 格式|INI-like keyfile 格式|
|**配置目录**|`/etc/sysconfig/network-scripts/`|`/etc/netplan/`|`/etc/NetworkManager/system-connections/`|
|**查看配置命令**|`nmcli con show`|`netplan get`|`nmcli con show`|
|**应用配置命令**|`systemctl restart network` 或 `nmcli con reload`|`netplan apply`|`nmcli con reload`|

编辑网卡配置文件

```
精简后的最小配置项：
TYPE=Ethernet
BOOTPROTO=none
DEVICE=ens33
ONBOOT=yes
IPADDR=172.25.250.81
NETMASK=255.255.255.0  # 或 PREFIX=24
GATEWAY=172.25.250.2
DNS1=114.114.114.114
```

用nmtui半图形化编辑

```
# 配置静态IP
nmcli con mod "ens33" ipv4.addresses "192.168.1.100/24"
nmcli con mod "ens33" ipv4.gateway "192.168.1.1"
nmcli con mod "ens33" ipv4.dns "8.8.8.8 8.8.4.4"
nmcli con mod "ens33" ipv4.method manual
nmcli con up "ens33"
```

### 6.3、常见网络问题与故障排除

- **IP地址冲突**：
    
- 当两个设备被分配相同的IP地址时，网络会出现冲突。
    
- **无法连接网络**：
    
- 检查网络配置（如IP地址、子网掩码、网关）是否正确。
    
- 使用`ping`命令测试网络连通性。
    
- **DHCP问题**：
    
- 检查DHCP服务器是否正常工作，客户端是否能获取到正确的IP地址。
    
- 物理故障：水晶头、交换机

七、磁盘分区

### 7.1、什么是磁盘分区表？

在理解MBR和GPT之前，我们先打个比方：

- **硬盘**就像一块空地
    
- **分区表**就像这张空地的“规划图纸”或“地图”
    
- **分区**就是按照图纸划分的不同区域（如：系统区、数据区、备份区）
    

**没有分区表** → 操作系统不知道如何管理硬盘空间 **有了分区表** → 操作系统才能“看懂”硬盘的结构

### 7.2、什么是MBR，什么是GPT

MBR=Master Boot Record（主引导记录）

**MBR结构**

```plain
MBR结构（位于磁盘最开始的512字节）：
┌─────────────────────────────┐
│   引导代码 (446字节)         │ ← 负责启动操作系统
├─────────────────────────────┤
│   分区表项1 (16字节)         │
│   分区表项2 (16字节)         │ ← 只能记录4个分区信息
│   分区表项3 (16字节)         │
│   分区表项4 (16字节)         │
├─────────────────────────────┤
│   结束标志 (0x55AA，2字节)   │ ← 有效性验证
└─────────────────────────────┘
总共：512字节
└─────────────────┘

方案A：创建4个主分区
方案B：创建3个主分区 + 1个扩展分区（逻辑分区）

扩展分区内部可以再划分逻辑分区（理论上无限个）
```

- 最大支持2.2TB磁盘（32位LBA × 512B扇区）
    
- 最多4个主分区（或3主+1扩展）
    
- 分区表无备份，易损坏
    
- 缺乏错误检测机制
    
- 适用于老电脑（传统BIOS启动或Windows 7或更老系统)
    

GPT=GUID Partition Table（全局唯一标识分区表）

```plain
GPT磁盘布局（设计非常聪明！）：
┌─────────────────────────────────────┐
│     保护性MBR（仅1个扇区）            │ ← 兼容层：防止旧工具误操作
│     作用：告诉旧系统“整个磁盘已分区”    │
├─────────────────────────────────────┤
│     主GPT头（LBA 1）                 │ ← 控制中心：包含CRC校验、分区表位置
│     包含：磁盘GUID、分区表位置、备份位置 │
├─────────────────────────────────────┤
│     主分区表（LBA 2-33）              │ ← 128个分区项（Windows默认）
│     每个分区项128字节，可记录详细分区信息│
├─────────────────────────────────────┤
│                                        │
│     实际分区内容（用户数据区）           │
│                                        │
├─────────────────────────────────────┤
│     备份分区表（磁盘尾部）              │ ← 完整备份！提高可靠性
├─────────────────────────────────────┤
│     备份GPT头（磁盘尾部最后1个扇区）     │ ← 备份的控制中心
└─────────────────────────────────────┘
支持超大容量（64位LBA，理论9.4ZB）（1ZB = 10亿TB）
分区数量几乎无限
完整的冗余备份机制
分区类型使用GUID，更灵活
```

**必须使用GPT的情况**：

- 硬盘大于2TB
    
- 新电脑（支持UEFI启动）
    
- 需要更多主分区
    
- 对数据安全性要求高
    

### 7.3、MBR vs GPT全面对比表

|特性|MBR|GPT|
|---|---|---|
|最大磁盘容量|2.2TB|9.4ZB|
|分区数量|4个主分区|128个（Windows默认）|
|分区表备份|无|有（头尾各一份）|
|兼容性|所有系统|较新系统（Win8+，Linux）|
|启动方式|BIOS/Legacy|UEFI|

### 7.4、启动流程对比

#### 传统BIOS + MBR启动流程

```plain
1. 开机 → BIOS自检
2. BIOS读取磁盘第一个扇区（MBR）
3. MBR中的引导代码执行
4. 找到活动分区（标记为启动的分区）
5. 加载活动分区的引导扇区
6. 启动操作系统
```

#### UEFI + GPT启动流程

```plain
1. 开机 → UEFI初始化
2. UEFI查找EFI系统分区（ESP）
3. 从ESP加载引导管理器（如：Windows Boot Manager）
4. 引导管理器读取BCD配置
5. 启动指定分区中的操作系统
```

### 7.5、怎么看磁盘是GPT还是MBR

```plain
fdisk -l

看到 Disklabel type: gpt → GPT分区
看到 Disklabel type: dos → MBR分区（Linux称MBR为"dos"标签）
```

### 7.6、fdisk分区

增加15G的硬盘，主动发现

centos7.9

```
for host in /sys/class/scsi_host/host*/scan; do
    echo "- - -" > $host
done
```

fdisk 分区

```
[root@c79-01 ~]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x194a9b7d.

Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   g   create a new empty GPT partition table
   G   create an IRIX (SGI) partition table
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)

Command (m for help): 

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): 
Using default response p
Partition number (1-4, default 1): 
First sector (2048-31457279, default 2048): 
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-31457279, default 31457279): +10G
Partition 1 of type Linux and of size 10 GiB is set

Command (m for help): p

Disk /dev/sdb: 16.1 GB, 16106127360 bytes, 31457280 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x194a9b7d

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048    20973567    10485760   83  Linux

Command (m for help): 
```

分区格式化

```
[root@c79-01 ~]# mkfs.ext4 /dev/sdb1
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
655360 inodes, 2621440 blocks
131072 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2151677952
80 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks: 
    32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done 
```

挂载

```
[root@c79-01 ~]# mkdir /mnt/sdb1
[root@c79-01 ~]# mount
mount       mount.fuse  mountpoint  
[root@c79-01 ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 979M     0  979M   0% /dev
tmpfs                    991M     0  991M   0% /dev/shm
tmpfs                    991M  9.6M  981M   1% /run
tmpfs                    991M     0  991M   0% /sys/fs/cgroup
/dev/mapper/centos-root   42G  2.2G   40G   6% /
/dev/sda1               1014M  168M  847M  17% /boot
tmpfs                    199M     0  199M   0% /run/user/0
[root@c79-01 ~]# mount /dev/sdb1 /mnt/sdb1
[root@c79-01 ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 979M     0  979M   0% /dev
tmpfs                    991M     0  991M   0% /dev/shm
tmpfs                    991M  9.6M  981M   1% /run
tmpfs                    991M     0  991M   0% /sys/fs/cgroup
/dev/mapper/centos-root   42G  2.2G   40G   6% /
/dev/sda1               1014M  168M  847M  17% /boot
tmpfs                    199M     0  199M   0% /run/user/0
/dev/sdb1                9.8G   37M  9.2G   1% /mnt/sdb1
[root@c79-01 ~]# 


开机启动挂载
#找uuid
[root@c79-01 ~]# blkid 
/dev/sr0: UUID="2020-11-04-11-36-43-00" LABEL="CentOS 7 x86_64" TYPE="iso9660" PTTYPE="dos" 
/dev/sda1: UUID="cbf99af4-1eca-4da0-b689-d7d86ed8a7a9" TYPE="xfs" 
/dev/sda2: UUID="1Ivcyk-cJsj-Vk2D-5cJU-7hpz-XBO0-xsvwee" TYPE="LVM2_member" 
/dev/sdb1: UUID="6bf78e5c-757d-4dee-a9bf-3f7e876719bf" TYPE="xfs" 
/dev/mapper/centos-root: UUID="5d3baeb8-4aee-4739-9134-25a6d6eb2f1a" TYPE="xfs" 
/dev/mapper/centos-swap: UUID="9acb52a5-88bc-4815-824f-9630b786081e" TYPE="swap" 


#再根据UUID进行挂载，不要漏掉文件格式，如xfs,ext4
[root@c79-01 ~]# vi /etc/fstab

#
# /etc/fstab
# Created by anaconda on Thu Dec 18 20:16:03 2025
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=cbf99af4-1eca-4da0-b689-d7d86ed8a7a9 /boot                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
UUID=6bf78e5c-757d-4dee-a9bf-3f7e876719bf /mnt/sdb1 xfs defaults  0 0
```