# 简单备份关键文件 
cp -r /boot /boot_backup  
cp -r /etc /etc_backup 

更新现有软件‌（确保系统状态健康）：
Ubuntu/Debian（类似手机应用商店更新）‌：
apt update && sudo apt upgrade -y  

CentOS/RHEL（类似安卓系统更新）
yum update -y    # CentOS 7 
dnf update -y    # CentOS 8以上

详细步骤
方法1：用系统自带的“应用商店”升级‌
----------------------------------Ubuntu/Debian系统（像安装普通软件一样简单）‌
查看当前内核版本：
uname -r   # 显示类似 5.4.0-100-generic

安装最新内核
sudo apt install linux-image-generic -y  
# 系统会自动下载最新稳定版内核

重启生效‌
reboot  
# 重启后输入 uname -r 检查是否升级成功

---------------------------------CentOS/RHEL系统（像系统自动推送更新）
下载内核rpm包
http://mirrors.coreix.net/elrepo-archive-archive/kernel/el7/x86_64/RPMS/

5.4.210-it
https://mirrors.coreix.net/elrepo-archive-archive/kernel/el7/x86_64/RPMS/kernel-lt-5.4.210-1.el7.elrepo.x86_64.rpm

5.4.210-it-devel
https://mirrors.coreix.net/elrepo-archive-archive/kernel/el7/x86_64/RPMS/kernel-lt-devel-5.4.210-1.el7.elrepo.x86_64.rpm

mkdir her
wget https://mirrors.coreix.net/elrepo-archive-archive/kernel/el7/x86_64/RPMS/kernel-lt-5.4.210-1.el7.elrepo.x86_64.rpm
wget https://mirrors.coreix.net/elrepo-archive-archive/kernel/el7/x86_64/RPMS/kernel-lt-devel-5.4.210-1.el7.elrepo.x86_64.rpm
rpm -Uvh kernel-lt
rpm -Uvh kernel-lt-devel
设置启动循序
grub2-set-default 0
重启 reboot

-------------------升级后检查--------------------
确认新内核生效‌：
uname -r   # 显示新版本号说明成功！

清理旧内核‌（像删除手机里的旧系统包）
Ubuntu/Debian‌：
apt autoremove --purge

CentOS/RHEL
package-cleanup --oldkernels --count=1
