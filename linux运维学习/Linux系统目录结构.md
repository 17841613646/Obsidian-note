### Linux的房间结构
一切从根分区开始
![[Pasted image 20260913093206.png]]
### 一切的起点根目录/
![[Pasted image 20260913093221.png]]

### /etc目录:系统配置文件中心
存放系统级配置文件，需要root用户修改
![[Pasted image 20260913093313.png]]

### /var 目录:动态的文件数据仓库
存放经常变化的数据
- /var/log:记录系统运行全过程
- /var/mail:存放待收发的邮件
![[Pasted image 20260913093433.png]]

### /usr 目录:软件仓库
存放应用程序
- /usr/bin(命令)
- /usr/lib(库)
![[Pasted image 20260913093636.png]]

![[Pasted image 20260913093928.png]]

### /home与/root: 用户的"私人房间"
/root管理员目录
/root用户目录
![[Pasted image 20260913094018.png]]

### 命令与硬件：核心目录扫盲
/bin:通用基础命令文件
/sbin:系统管理命令
/dev：设备目录（鼠标硬件在linux皆为文件）
![[Pasted image 20260913094132.png]]

### Linux的核心哲学：一切皆为文件

![[Pasted image 20260913094258.png]]


![[Pasted image 20260913094552.png]]

### 删除/home基础文件如何复原

![[Pasted image 20260913094657.png]]

### 为什么要遵循FHS标准？

![[Pasted image 20260913094712.png]]