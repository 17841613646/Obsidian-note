### 什么是SSH
SSH是一种远程传输加密协议，通过ssh隧道可以远程操作虚拟机的命令行

![[Pasted image 20260912203830.png]]

### 为什么使用SSH
Telnet：明文传输，极易被监听窃取信息
SSH：全过程加密，保障远程管理安全
telnet标准接口：23 ssh：22
![[Pasted image 20260912204122.png]]

### SSH架构核心
服务器端：会后台运行sshd服务监听连接请求
客户端：安装ssh客服端工具
核心组件：工业标准的openssh实现
![[Pasted image 20260912204455.png]]

### 如何获得linux虚拟机IP
在命令行输入 ip addr
![[Pasted image 20260912204611.png]]

### 主流远程连接工具

目前使用HexHub：
[[HexHub]]基本使用方法
![[Pasted image 20260912204845.png]]

备选：

![[Pasted image 20260912204704.png]]


### 总结
![[Pasted image 20260912205222.png]]

相关题目
1c
![[Pasted image 20260912205340.png]]
2.abc
![[Pasted image 20260912205402.png]]
3
![[Pasted image 20260912205304.png]]