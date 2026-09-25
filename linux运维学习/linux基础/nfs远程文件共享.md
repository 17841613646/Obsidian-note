# NFS 服务部署与实战指南

## 一、 NFS 核心概念

NFS（Network File System，网络文件系统）允许 Linux 服务器之间通过网络共享目录，就像使用本地磁盘一样。它是企业内网中实现**文件共享、多台 Web 服务器共享静态资源**的标准方案。

## 二、 服务端部署（提供共享）

**1. 安装服务端软件包**

bash

yum install nfs-utils -y    # CentOS/Rocky 系统
sudo apt install nfs-kernel-server -y  # Ubuntu/Debian 系统

**2. 创建共享目录**

bash

mkdir -p /export/share

**3. 配置共享规则（`/etc/exports`）**

bash

vim /etc/exports

添加如下内容（注意 IP 段和权限）：

ini

/export/share 192.168.113.0/24(rw,sync,no_root_squash)

**参数详解：**

- `rw`：读写权限（`ro` 为只读）。
    
- `sync`：数据同步写入磁盘（保证数据不丢失，生产环境推荐；`async` 速度快但断电易丢数据）。
    
- `no_root_squash`：客户端的 root 用户登录后保留 root 权限（**⚠️ 生产环境慎用**，通常建议用默认的 `root_squash` 将 root 映射为匿名用户）。
    
- `192.168.113.0/24` 是一个 **CIDR（无类别域间路由）网段表示法**。

在这里，它的作用是控制“哪些机器”有权限访问这个 NFS 共享目录。

**4. 启动服务并设置开机自启**

bash

systemctl start nfs-server
systemctl enable nfs-server

_⚠️ 注意：服务端还需要确保 `rpcbind` 服务正常运行（NFSv3 依赖它），并配置防火墙放行 NFS 相关端口（NFSv4 只需 2049/TCP）。_

---

## 三、 客户端部署（挂载共享）

**1. 安装客户端软件包**

bash

yum install nfs-utils -y

**2. 创建本地挂载点**

bash

mkdir -p /mnt/nfs

**3. 临时挂载（重启后失效）**

bash

mount -t nfs 192.168.133.128:/export/share /mnt/nfs

**4. 永久挂载（开机自动连接）**

bash

vim /etc/fstab

添加一行：

ini

192.168.133.128:/export/share  /mnt/nfs  nfs  defaults  0  0

**测试配置是否正确：**

bash

mount -a

无报错即代表配置成功。可以用 `df -h | grep nfs` 验证。

---

## 四、 验证与检查

1. **服务端创建测试文件**：在服务端的 `/export/share` 下创建两个文件。
    
2. **客户端查看**：在客户端执行 `ls /mnt/nfs`，能看到文件即代表成功。
    
3. **查看连接状态**：`mount -t nfs`（显示所有已连接的 NFS 共享）。
    

---

## 五、 取消挂载与强制卸载

bash

umount /mnt/nfs             # 正常卸载
umount -f /data/static      # 强制卸载（当文件系统卡死时）
umount -l /data/static      # 懒惰卸载（立即断开路径，等不再使用后再清理）

_💡 提示：如果提示 `device is busy`，说明有进程正在使用该目录。可用 `lsof +D /mnt/nfs` 或 `fuser -mv /mnt/nfs` 找出占用进程。_

---

## 六、 高级挂载参数（生产环境调优）

针对网络抖动或服务端故障场景，可以使用软挂载来防止客户端卡死：

bash

mount -o soft,timeo=5,retrans=3 172.25.250.163:/data/nfs_share /data/static

**参数逐字拆解：**

- `-o`：指定挂载选项。
    
- `soft`：**软挂载**。服务端无响应时，客户端会在超时后直接报错（I/O error），而不是无限等待（防止 Nginx 等进程卡死）。
    
- `timeo=5`：超时时间。**单位是十分之一秒**，`5` 代表 **0.5 秒**。
    
- `retrans=3`：重试次数。总共尝试 `0.5秒 * 3次 = 1.5秒`，超时后客户端放弃并报错。
    
- `172.25.250.163:/data/nfs_share`：服务端 IP 和共享目录。
    
- `/data/static`：客户端本地挂载点。
    

_⚠️ 生产环境选型建议：`soft` 配合短超时适合 Web 静态资源等“快速失败”场景，但存在网络抖动导致数据写入中断甚至损坏的风险。对数据一致性要求极高的场景（如数据库），必须使用 `hard`（默认）挂载。_

---

## 七、 ⚠️ 运维避坑与核心注意事项

1. **UID/GID 一致性问题（你踩过的坑）**：NFS 默认基于 UID 数字匹配。如果客户端和服务端的 `sky` 用户 UID 不一致（比如一个是 1001，一个是 1002），客户端写入文件会报 `Permission denied`。**解决：在两台机器上用 `usermod -u 1001 sky` 统一 UID。**
    
2. **权限分配**：共享目录的属组和权限要匹配需求，千万别随意 `chmod 777`。
    
3. **防火墙规则**：服务端需放行 `nfs`、`mountd`、`rpc-bind` 服务，或直接放行 NFSv4 的 `2049/tcp` 端口。
    
4. **`no_root_squash` 风险**：开启后，客户端 root 可以随意修改服务端文件，极易引发安全问题。生产环境建议保持默认的 `root_squash`，配合普通用户 + `sudo` 管理。
    
5. **`sync` vs `async`**：生产环境务必使用 `sync`，虽然性能略低，但能保证断电或宕机时数据不丢失。