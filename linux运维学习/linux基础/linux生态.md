## Linux生态

### 1.1linux定义
linux发行版 = linux内核+GNU 工具链(gcc/automake/make/glibc/bash)+应用软件(浏览器/办公软件/GUI软件/web服务软件)
GNU计划：做一款完全自由免费的操作系统


### 1.2发行版
正统红帽系（redhat）:Fedora/centos(centos stream)/redhat(REHL) 8.10/9.7/10.1
衍生红帽系:Rockylinux/Almalinux/OpenEuler(欧拉)/银河麒麟
Debian系：Debian/Ubuntu/优麒麟
suse系： openSUSE
bsd系：openBSD

### 2.4虚拟网络
桥接:
虚拟机就像1台物理机一样,它可以上网.可以被其他人访问

![[Pasted image 20260921184132.png]]

NAT模式：
虚拟机在虚拟路由器之后,IP地址由vm提供，可以上网，可以被物理母机访问，但不能被其他人（局域网）访问

![[Pasted image 20260921184227.png]]

仅主机模式：虚拟机就像1台独立的物理机,他不能上网,也不能被其他人访问
使用场景：信息安全测试
![[Pasted image 20260921184425.png]]