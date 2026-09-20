
![[Pasted image 20260919160604.png]]


PID1进程是负责启动和管理操作系统的所有环境和服务
采用并发执行

![[Pasted image 20260919160623.png]]

### systemctl基础语法
systemctl \[动作]\[服务名] 
systemctl start sshd

![[Pasted image 20260919160731.png]]

### systemctl status服务的详细信息

loaded：配置文件路径以及加载状态
active：核心运行状态
main pid：服务主进程ID标识符

![[Pasted image 20260919160856.png]]

### 开机自启
- enable：创建符号链接，实现自启动
- disable：移除符号链接：取消自启动
- is-enable:检查当前配置状态

![[Pasted image 20260919161050.png]]

![[Pasted image 20260919161233.png]]

### 管理SSHD服务

![[Pasted image 20260919161247.png]]

### 常见问题

![[Pasted image 20260919161319.png]]

### 总结

![[Pasted image 20260919161340.png]]