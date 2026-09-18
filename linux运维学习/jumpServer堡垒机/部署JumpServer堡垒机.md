# JumpServer 堡垒机部署实战笔记（前4步）


# 堡垒机的作用

请看[[企业安全基石：堡垒机（JumpServer）核心作用解析]]
## 第1步：JumpServer 简介

### 📌 核心知识点

- **定位**：广受欢迎的开源堡垒机，符合 **4A 规范**（认证 Authentication、授权 Authorization、账号 Account、审计 Audit）。
    
- **核心功能**：事前授权（谁能连哪台机器）、事中监察（命令拦截、实时监控）、事后审计（操作录屏、历史追溯）。
    
- **核心价值**：满足企业等保合规要求，防范内部运维人员误操作或恶意删库。
    

## 第2步：基础系统环境配置

### 📌 核心知识点

- **系统选择**：Rocky Linux（兼容 RHEL/CentOS）、Ubuntu 等。
    
- **IP 地址配置**（使用 `nmcli` 工具）：
    
    - `ip addr`：查看网卡名称（如 `ens160`）。
        
    - `nmcli connect mod ens160 ipv4.address 192.168.x.x/24`：设置静态 IP。
        
    - `nmcli connect mod ens160 ipv4.gateway 192.168.x.1`：设置网关。
        
    - `nmcli connect mod ens160 ipv4.dns 223.5.5.5`：设置 DNS（推荐阿里或腾讯 DNS）。
        
    - `nmcli connect mod ens160 ipv4.method manual`：设置为手动模式。
        
    - `nmcli connect up ens160`：激活生效。
        
- **关闭防火墙和 SELinux**（实验环境特有，生产环境需严格配置策略）：
    
    - 关闭防火墙：`systemctl stop firewalld && systemctl disable firewalld`。
        
    - 关闭 SELinux：`setenforce 0`（临时）或修改 `/etc/selinux/config` 为 `disabled`（永久）。
        

### 🚨 踩坑与高光记录

1. **SELinux 拦截**：在修改 SSH 端口（22 -> 3222）时，如果 SELinux 处于 `Enforcing` 状态，重启 SSH 服务**必定报错**（`Job for sshd.service failed`）。解决办法：`sudo setenforce 0` 并永久关闭 SELinux。
    
2. **VMware NAT 网络问题**：宿主机 ping 不通虚拟机。排查步骤：检查 Windows 的 `VMnet8` 网卡是否存在 -> 勾选“将主机虚拟适配器连接到此网络” -> 检查 VMware NAT 服务是否运行 -> 检查 VMware 子网 IP 与虚拟机内 IP 是否一致。
    
3. **DNS 解析失败**：手机热点或校园网可能屏蔽 `114.114.114.114`，改用 `223.5.5.5`（阿里）或 `119.29.29.29`（腾讯）。如果 `ping 8.8.8.8` 通但 `ping baidu.com` 报错，说明是 DNS 问题。
    

## 第3步：安装基础软件

### 📌 核心知识点

- **安装命令**：`yum install vim tar net-tools wget curl gettext iptables -y`。
    
- **工具用途**：
    
    - `vim`：文本编辑器（服务器无图形界面，全靠它改配置）。
        
    - `tar`：解压工具。
        
    - `net-tools`：包含 `ifconfig` 等老的网络工具（可选）。
        
    - `wget`/`curl`：命令行下载工具 / API测试工具。
        
    - `gettext`：多语言适配（JumpServer 汉化依赖）。
        
    - `iptables`：底层防火墙工具。
        

### 🚨 踩坑与高光记录

- **下载源的区别（企业级思维）**：Linux 中 `wget` 只能下载**直链**文件。对于百度网盘，因为需要登录和验证，无法用 `wget` 直接下载，需要在 Windows 下载后使用 Xftp/WinSCP 上传。这模拟了企业**“办公网下载 -> 堡垒机上传 -> 生产网部署”**的真实闭环。
    
- **目录规范**：上传文件放个人家目录 `/home/xxp/`，正式部署必须移到 `/opt/` 目录下，绝不在根目录 `/` 下乱建文件夹。
    

## 第4步：JumpServer 安装

### 📌 核心知识点

#### **上传JumpServer安装包**

![[Pasted image 20260918164004.png]]

- **解压与安装**：`tar -zxvf jumpserver-ce-*.tar.gz` 解压后，执行 `./jmsctl.sh install` 启动官方安装脚本。

![[Pasted image 20260918164041.png]]

## **脚本互动配置**

**配置持久化目录**

**是否需要自订持久化储存的路径？不自订将使用默认目录 /data/jumpserver? [y/N]:** **n**

**完成**

**3. 配置数据库**

**是否使用外部 PostgreSQL? [y/N]:** **n**

**完成**

**4. 配置 Redis**

**请输入 Redis 模式 [redis/sentinel] (default: redis):**              

**是否使用外部 Redis? [y/N]:** **n**

**完成**

**5. 配置外部访问**

**是否需要配置 JumpServer 对外访问端口? [y/N]:** **n**

**完成**

**7. Configure Others**

**Please enter language [zh/en/ja/es/ko/ru/vi] (default: zh):** **回车**

**Please enter timezone [default: Asia/Shanghai] :** **回车**

**完成**


- **互动配置选项（默认安全选择）**：
    
    - 持久化目录、外部 PostgreSQL、外部 Redis：选 `n`（使用内置）。
        
    - Redis 模式：直接回车（`redis`）。
        
    - 语言和时区：直接回车（`zh` / `Asia/Shanghai`）。
        
- **端口冲突配置（核心）**：如果服务器上有 Zabbix/Nginx 占用 80 端口，必须配置 `HTTP_PORT=8080` 以共存。
    
- **域名配置（v5 特性）**：修改 `/opt/jumpserver/config/config.txt` 里的 `DOMAINS`。
    
- **启动命令**：`sudo ./jmsctl.sh start`。
    

### 🚨 踩坑与高光记录

1. **端口冲突（惨痛教训）**：JumpServer 默认使用 80 端口。有 Zabbix 时，启动会报 `address already in use`。解决方法：
    
    - 方法一（推荐）：`./jmsctl.sh install` 时，在“是否需要配置 jumpserver 对外访问端口?” 输入 `y` 并填 `8080`。
        
    - 方法二：直接改 `/opt/jumpserver/config/config.txt` 里的 `HTTP_PORT=8080`。
        
2. **DOMAINS 铁律（v5 特性）**：网页登录报“配置文件存在问题，无法登录”。在 `config.txt` 中配置 `DOMAINS` 时，**千万不能带端口号**（写 `DOMAINS=192.168.x.x` 而不是 `:8080`），端口由 `HTTP_PORT` 负责。改完必须 `sudo ./jmsctl.sh restart`。
    
3. **Ansible 隔离报错**：测试资产连接报错 `未找到 Ansible Docker 镜像“jumpserver/ansible-executor:latest”`。因为离线包缺少该镜像，直接去“系统设置 -> 功能设置 -> 作业中心”，关闭 **“Ansible Docker 隔离”** 即可。
    
4. **SSH 端口修改铁律**：改 `/etc/ssh/sshd_config` 端口前：① 备份文件；② 用 `sshd -t` 检查语法；③ 解决 SELinux 拦截；④ 重启 `sshd`。
    
5. **纳管 Linux 报“认证被拒绝”**：检查目标机器 `/etc/ssh/sshd_config`，确保有 `PermitRootLogin yes` 和 `PasswordAuthentication yes`，并重启 `sshd`。
    

---

### 💡 笔记使用小贴士

- 建议在笔记中加一个**“四要四不要”**的醒目标签：
    
    - **不要**用 `rm -rf /*` 测试拦截，**要**用 `echo "test"` 或 `rm -rf /tmp/test`。
        
    - **不要**让普通用户做特权操作，**要**用 `sudo` 或普通用户 + 授权。
        
    - **不要**在根目录 `/` 下随意建目录，**要**规范使用 `/home/xxp`（临存）和 `/opt`（部署）。
        
    - **不要**忽视 SELinux，**要**在修改敏感服务端口前先 `setenforce 0`。
        

去体验 SFTP 文件传输吧，项目文档的完整通关就在眼前了！有任何问题随时找我，我们一起把剩下的尾巴收好！加油！


## 第5步：登录 JumpServer 控制台

### 📌 核心知识点

![[Pasted image 20260918164107.png]]

- **启动服务**：在安装目录下执行 `./jmsctl.sh start`，等待 8 个 Docker 容器全部变成绿色（`Started` 或 `Running`）。
    
- **访问地址**：`http://<你的服务器IP>:<端口>`（默认是 80，如果改了端口就是 8080）。
    ![[Pasted image 20260918164113.png]]
- **初始凭据**：默认账号 `admin`，默认密码 `ChangeMe`，**首次登录必须强制修改密码**（强密码要求包含大小写、数字、特殊符号）。
    ![[Pasted image 20260918164125.png]]
![[Pasted image 20260918164134.png]]
### 🚨 踩坑与高光记录

1. **端口冲突**：JumpServer 默认抢占 80 端口。如果服务器上装了 Zabbix/Nginx，必须改成 `HTTP_PORT=8080`，并在浏览器访问时带上 `:8080`。
    
2. **DOMAINS 铁律（v5 特性）**：如果页面报“配置文件存在问题，无法登录”，需要修改 `/opt/jumpserver/config/config.txt`。**注意：v5 版本里 `DOMAINS` 只写 IP，绝对不要带端口号**（写 `DOMAINS=192.168.44.129`）。改完必须 `./jmsctl.sh restart` 才会生效。
    
3. **浏览器缓存**：每次重启后如果页面依旧报错，按 `Ctrl+Shift+N` 用无痕模式访问，避免浏览器缓存坑人。
    

## 第6步：配置资产管理

### 📌 核心知识点
![[Pasted image 20260918164159.png]]
- **建节点**：相当于在电脑里建“文件夹”。在“资产管理 -> 资产列表”里，右键默认节点，选择“创建节点”。（如：`长沙分公司`、`实验室服务器`）。
    ![[Pasted image 20260918164205.png]]
- **纳管 Linux 资产**（填基本信息）：
    ![[Pasted image 20260918164232.png]]
    - 名称：`Rocky-Linux-堡垒机测试服务器`
        
    - IP 主机：`192.168.44.129`（被控的真实服务器 IP）
        
    - 平台：`Linux`
        
    - 节点：选择刚建的节点。
        
    - 协议：默认 `ssh`（22）和 `sftp`（22）。
        
    - 账号：填真实被控服务器的 `root` 密码，勾选“特权账号”。
        
- **纳管网络设备**（如华为 CE12800）：需要设备提前开启 SSH、配置 AAA 和本地管理员。在 JumpServer 里选择“网络设备” -> “华为”平台，填 IP、协议和账号。
    

### 🚨 踩坑与高光记录

1. **Ansible 隔离报错（必踩）**：测试连接时报错 `未找到 Ansible Docker 镜像“jumpserver/ansible-executor:latest”`。**解决办法**：去“系统设置 -> 功能设置 -> 作业中心”，关闭 **“Ansible Docker 隔离”**。
    
2. **SSH 认证被拒绝**：报错“认证被拒绝”。去目标机器（`192.168.44.129`）检查 `/etc/ssh/sshd_config`，确保有 `PermitRootLogin yes` 和 `PasswordAuthentication yes`，然后 `sudo systemctl restart sshd`。
    
3. **修改 SSH 端口铁律（血泪教训）**：如果想改默认的 22 端口（如改成 3222），**必须**：① 先备份文件；② 改完用 `sudo sshd -t` 检查语法；③ **解决 SELinux 拦截**（`sudo setenforce 0`），否则重启 sshd 一定会报错；④ 修改成功后，**同步修改 JumpServer 资产里 ssh 和 sftp 的端口**为 3222。
    

## 第7步：增加用户及授权

### 📌 核心知识点
![[Pasted image 20260918164245.png]]
- **创建用户（RBAC模型）**：
    ![[Pasted image 20260918164254.png]]
    - 名称：`系统运维/小张`，用户名 `zhangsan`。
        
    - 系统角色：普通用户必须选 **“用户”**（如果没有，选“系统操作员”），**绝不能给管理员**。
        
- **配置资产授权**：
    ![[Pasted image 20260918164301.png]]
    - 在“授权管理 -> 资产授权 -> 创建”。
        ![[Pasted image 20260918164317.png]]
    - 把“用户”（`zhangsan`）和“资产”或“节点”（`Rocky-Linux`）绑定起来。
        
- **验证授权（站在用户角度）**：退出 `admin` -> 用 `zhangsan` 登录 -> 去“我的资产” -> 点击资产“连接” -> 弹出 Web 终端。
    

### 🚨 踩坑与高光记录

1. **命令过滤生效条件（面试考点）**：想测试高危命令拦截（如 `rm -rf /*`），必须确保：① **普通用户（非 admin）** 登录；② **规则已绑定到资产**；③ **通过网页 Web 终端**执行（Xshell 直连无效）。测试命令推荐使用 `echo "test"` 或 `rm -rf /tmp/test`。
    
2. **拦截图谱与误区**：`echo "test"` 被拦截时，提示可能显示“命令 `rm -rf /` 是被禁止的”（因为底层是精确匹配/子字符串匹配规则）。看到红色的“被禁止”即代表成功。
    
3. **验证免死金牌**：在测试破坏性命令前，**一定要先拍虚拟机快照**，测试完回滚快照。千万不要在真实生产环境上做这种测试。

# JumpServer 堡垒机部署实战笔记（第8步）

## 第8步：提升安全性（安全加固与命令过滤）

这一步是让堡垒机真正发挥“安全防御”作用的关键。主要包括：修改默认端口、限制登录源IP、以及拦截高危命令。

### 8.1 修改默认 SSH 端口（隐蔽性加固）

- **核心目的**：将默认的 `22` 端口改为 `3222`，避开互联网上针对 22 端口的自动化扫描和暴力破解。
    
- **标准操作流程**：
    
    1. **备份文件**：`sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak`（**铁律：改配置前必备份**）。
        
    2. **修改端口**：`sudo sed -i 's/^#Port 22/Port 3222/' /etc/ssh/sshd_config`。
        
    3. **检查语法**：`sudo sshd -t`。
        
    4. **重启服务**：`sudo systemctl restart sshd`。
        
- **🚨 踩坑记录（致命错误）**：
    
    - **SELinux 拦截**：改完端口重启 `sshd` 报错 `Job for sshd.service failed`，大概率是 SELinux 拦截了非标准端口。解决办法：实验环境直接 `sudo setenforce 0` 临时关闭并永久关闭 SELinux。生产环境需用 `semanage port -a -t ssh_port_t -p tcp 3222` 放行。
        
    - **保命提示**：修改端口前，**必须开着 VMware 控制台窗口**，防止 SSH 断开后彻底连不上虚拟机。
        
    - **同步更新**：改完 Linux 端口后，**必须回 JumpServer 网页端**，把对应资产的 `ssh` 和 `sftp` 协议端口，全部从 `22` 改为 `3222`。
        

### 8.2 限制管理员地址（IP 白名单）

- **核心目的**：利用 SSH 白名单，只允许堡垒机的 IP 连接服务器，强制切断运维人员绕过堡垒机直连服务器的可能。
    
- **命令**：`echo "AllowUsers *@10.1.1.201" >> /etc/ssh/sshd_config`
    
- **🚨 踩坑记录（极度危险）**：
    
    - 文档里的 `10.1.1.201` 是教程作者独立堡垒机的 IP。你的环境是**本机 Docker 部署**，源 IP 会变成 Docker 网关的 `172.x.x.x`。
        
    - 如果在你的实验环境直接抄这行命令，你会**瞬间把自己锁死**。建议**实验环境直接跳过此步**。
        
    - 企业真实做法是：用防火墙（firewalld/iptables）做 IP 白名单，而不是改 SSH 配置。因为改 SSH 配置一旦出错，很难救回来。
        

### 8.3 过滤高危命令（事中监察）
![[Pasted image 20260918164336.png]]

玩笑

rm  删除        -rf强制删除     /*全盘文件

- **核心目的**：防止运维人员手抖敲下 `rm -rf /*`、`shutdown`、`reboot` 等毁灭性命令。
    
- **操作流程**：
    ![[Pasted image 20260918164404.png]]
    1. **创建规则**：“系统设置 -> 安全 -> 命令过滤 -> 命令组创建”。
        
    2. **规则建议（强烈建议用正则）**：
        
        - 拦截删除：`^rm\s+-rf.*`
            
        - 拦截关机重启：`^(reboot|shutdown).*`
    3. **绑定资产**：去“资产管理 -> 编辑资产 -> 命令过滤”，选中规则并提交。
            ![[Pasted image 20260918164429.png]]
        
- **测试法则（用血的教训总结）**：
    
    - **绝对禁止**用真实的 `rm -rf /*` 测试！
        
    - **安全测试命令**：用 `echo "test"` 或 `rm -rf /tmp/test`。
        
    - **生效条件**：必须使用**普通用户（如 zhangsan）**在**网页终端**测试。`admin` 账号通常不受限，Xshell 直连可以绕过（因为流量不经过堡垒机）。
        
    - **拦截提示的误导**：测试 `rm -rf /tmp/test` 时，提示可能显示“命令 `rm -rf /` 是被禁止的”，只要看到红色的“被禁止”，就代表拦截成功。
        

### 💡 第8步核心升华（面试考点）

安全加固不是乱用命令，而是建立**纵深防御体系**：

1. **网络层**：防火墙白名单，只允许堡垒机 IP 访问目标服务器，防止绕过堡垒机。
    
2. **系统层**：修改默认端口、关闭不必要的服务。
    
3. **应用层**：JumpServer 命令过滤，进行事中拦截。
    
4. **数据层**：核心文件加 `chattr +i` 锁，定期做数据库备份。


# JumpServer 堡垒机部署实战笔记（第9步）

## 第9步：文件传输配置（Web SFTP）

### 📌 核心知识点
![[Pasted image 20260918164443.png]]
- **什么是 Web SFTP**：JumpServer 利用网页终端组件（Koko）代理了 SFTP 协议。你在网页上拖拽文件，底层其实是堡垒机替你通过 SFTP 协议连到了目标服务器的 22 端口（或你修改后的 3222 端口）。
    
- **优势**：无需在本地安装 Xftp，无需知道服务器密码，且所有文件传输操作都有日志记录。
    
- **TFTP（扩展了解）**：一种基于 UDP 的轻量级文件传输协议，常用于网络设备（如华为交换机）备份 `vrpcfg.cfg` 配置文件。命令如 `tftp 10.1.1.201 put vrpcfg.cfg`。
    

### 🛠️ 操作流程（普通用户视角）

**第一步：打开文件管理界面**

1. 用 `zhangsan` 登录 JumpServer，进入“我的资产”，连接你的 Rocky Linux 服务器。
    
2. 在弹出的 **Web 终端窗口** 顶部（或左侧边栏），找一个**文件夹图标**，通常叫 **“文件管理”** 或 **“SFTP”**。
    
3. 点击后，会弹出一个左右分栏的文件管理界面（左边是你 Windows 本地，右边是 Linux 服务器）。
    

**第二步：上传文件（Windows -> Linux）**

1. 在右侧（Linux 目录树）中，**进入 `/tmp` 目录**（强烈建议新手先传到 `/tmp`，因为普通用户没有权限往 `/root` 目录写文件）。
    
2. 从左侧（Windows）选中一个小文件（比如 `test.txt`），直接**拖拽**到右侧，或者点击“上传”按钮选择文件。
    
3. 上传完成后，在 Web 终端里敲 `ls /tmp`，确认 `test.txt` 已经在服务器上了。
    

**第三步：下载文件（Linux -> Windows）**

1. 在右侧找到服务器上的一个文件（比如 `/var/log/messages`）。
    
2. 选中它，点击“下载”按钮，或者直接拖拽到左侧 Windows 目录，文件就会下载到你本地电脑。
    

### 🚨 踩坑与高光记录（极其重要）

1. **端口必须同步（血泪教训）**：如果你在第8步修改了 SSH 端口（22 -> 3222），**必须确保 JumpServer 网页里这个资产的 `sftp` 协议端口也改成了 3222**。否则文件管理会报“连接失败”或“认证错误”。
    
2. **权限问题**：普通用户 `zhangsan` 通过 SFTP 上传文件时，如果传到 `/root` 或 `/etc` 目录，会报 `Permission denied`。建议上传到 `/tmp`，或者让管理员 `admin` 去传。这也是 Linux 最小权限原则的体现。
    
3. **文件大小限制**：JumpServer 默认对 Web SFTP 上传大文件有限制（通常是几百 MB）。如果传大文件（如几个 G 的安装包），建议用 Xshell 的 Xftp 或命令行 `scp`（但那是绕过堡垒机的行为，生产环境禁止）。
    
4. **审计功能**：你在 Web SFTP 里上传、下载、删除文件的动作，都会被 JumpServer 记录在“文件传输”日志里。管理员随时能查到你什么时候传了什么文件，这是企业安全审计的关键一环。
    

### 💡 总结升华（面试考点）

文件传输安全是企业数据防泄漏（DLP）的重要一环。通过 JumpServer 的 Web SFTP，企业实现了：

- **通道统一**：所有文件传输必须过堡垒机，禁止直连。
    
- **权限控制**：普通用户只能传文件到指定目录，无法触碰核心配置文件。
    
- **事后追溯**：谁传了恶意脚本？谁下载了核心数据？一查日志便知。
    

---

### 🎉 项目收官总结

从 Rocky Linux 基础环境搭建，到 Docker 离线部署，再到端口冲突、SELinux 拦截、资产纳管、权限分配、命令过滤，最后到 SFTP 文件传输。你不仅跑通了一个完整的 JumpServer 项目，还经历了“删库惊魂”和“科学排错”的洗礼。

**这段经历可以直接写进你的简历：**

> “独立完成基于 Rocky Linux 9 的 JumpServer v5.0.0 堡垒机离线部署与配置。解决端口冲突、SELinux 限制、SSH 认证等底层问题；基于 RBAC 完成用户与资产授权；配置正则命令过滤规则，成功在 Web 终端拦截 `rm -rf` 等高危操作；实现基于 Web SFTP 的安全文件传输。”

去网页里找一下那个文件夹图标，成功把一个小文件拖进去，然后敲 `ls /tmp` 看到它，你的堡垒机项目就正式**大圆满通关**了！