nfs服务部署

服务端
1. 安装服务端
yum install nfs-utils -y

2. 创建共享文件夹
mkdir /export/share

3. 设置共享规则
、# vim /etc/exports    、#编辑nfs配置文件
共享目录		   允许访问的机器	 	访问者的权限
/export/share 192.168.113.0/24(rw,sync,no_root_squash) 
rw： 读写
sync： 保存
no_root_squash： 访问者的root用户有管理员权限

4.启动服务
systemctl start nfs-server   
systemctl enable nfs-server	、#开机自启

客户端
1. 安装客户端
yum install nfs-utils -y

2. 挂载（分为临时挂载和永久挂载）
临时挂载---#关机
mount -t nfs 192.168.133.128:/export/share /mnt/nfs
把192.168.133.128的/export/share目录挂载到本地的 /mnt/nfs

永久挂载---#（开机自动连接）
vim /etc/fstab  # 编辑开机自动挂载列表  
添加一行：
192.168.133.128:/export/share  /mnt/nfs  nfs  defaults  0  0 
测试命令： mount -a（无报错即成功）

3.检查是否成功
在服务端的共享文件夹中创建两个文本文件
在客户端查看 
ls /mnt/nfs

查看连接状态‌：
mount -t nfs  # 显示所有已连接的NFS共享

