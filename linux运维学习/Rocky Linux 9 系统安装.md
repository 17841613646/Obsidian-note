### 课程引导
![[Pasted image 20260912160200.png]] 
### 安装前的三个关键决策
1.合理划分磁盘空间
2.选择dhcp自动获取或手动配置静态IP
3.设置强root密码
![[Pasted image 20260912160408.png]]

### 启动安装程序
1.挂载镜像
2.启动电源
3.开始安装
![[Pasted image 20260912160512.png]]

### Rocky Linux 9 的下载
打开[Download - Rocky Linux](https://rockylinux.org/download)官网
下载安装Rocky Linux 9 
三种iso镜像的区别：
1.DVD ISO带GUI图形界面的安装
2.BOOT ISO (网络引导版）
3.Minimal ISO（最小化安装版）
![[Pasted image 20260912160653.png]]

![[Pasted image 20260912162244.png]]
### 安装启动镜像
Root账号锁定：不能直接登录root账户
如果想要管理员权限，可以创建一个用户给他管理员权限

![[Pasted image 20260912161519.png]]
![[Pasted image 20260912161652.png]]

安装时只选择最小安装
![[Pasted image 20260912161723.png]]

手动配置静态ip地址，保证IP不会变更
![[Pasted image 20260912161800.png]]

自定义存储分配

![[Pasted image 20260912161904.png]]

![[Pasted image 20260912161927.png]]


手动设置boot启动分区，swap交换分区，根分区
![[Pasted image 20260912161946.png]]

### 执行安装与重启
如果发生重复启动安装的问题，把iso镜像移除
![[Pasted image 20260912162504.png]]

### 首次登录与系统验证

1.终端登录 root
2.测试网络联通情况 ping -c 4 baidu.com
3.地址查看  ip addr show 缩写 ip a

![[Pasted image 20260912162609.png]]

我的密码
![[Pasted image 20260912170347.png]]
### 小结

![[Pasted image 20260912162758.png]]


问题
如果没有vm8网卡
![[Pasted image 20260912202319.png]]

## 一、 问题 1：开机报错“客户机操作系统已禁用 CPU”

**现象**：虚拟机开机即弹出报错提示，无法进入系统。  
**原因**：Windows 宿主机开启了 Hyper-V、WSL2（适用于 Linux 的 Windows 子系统）或“虚拟机平台”。这些微软自带的虚拟化功能会抢占 CPU 的硬件虚拟化权限（VT-x/AMD-V），导致 VMware 拿不到底层权限。  
**解决方法**：

1. 打开 Windows 的“启用或关闭 Windows 功能”。
    
2. **取消勾选**：`Hyper-V`、`Windows 虚拟机监控程序平台`、`适用于 Linux 的 Windows 子系统`、`虚拟机平台`。
    
3. 点击确定，**重启电脑**。
    

## 二、 问题 2：宿主机没有 VMnet8 虚拟网卡

**现象**：在 Windows 的 CMD 中输入 `ipconfig`，找不到 `VMware Network Adapter VMnet8`，导致宿主机无法与虚拟机通信。  
**原因**：Hyper-V 冲突导致网卡驱动无法创建，或 VMware 安装残留损坏了注册表。  
**解决方法**：

1. **先做**：在 VMware 菜单栏 -> “编辑” -> “虚拟网络编辑器” -> “更改设置” -> 点击“**还原默认设置**”。
    
2. **终极方案（清理注册表）**：
    
    - 彻底关机，按 `Win + R` 输入 `regedit`。
        
    - 找到路径：`HKEY_LOCAL_MACHINE\SOFTWARE\Classes\CLSID`
        
    - 找到项：`{3d09c1ca-2bcc-40b7-b9bb-3f3ec143a87b}`（VMware 残留虚拟网卡注册表）。
        
    - **右键导出备份**，确认备份成功后，右键删除。
        
    - 重启电脑，以管理员身份运行 VMware，再次点击“还原默认设置”。