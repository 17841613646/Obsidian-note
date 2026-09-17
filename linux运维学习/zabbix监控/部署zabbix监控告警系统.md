## 1. 基础系统环境准备（初始化）

全新安装 openEuler（或 CentOS 9 / Rocky 9）后，首要任务是配置网络并关闭安全机制。

### 1.1 配置 IP 地址
设置静态 IP、网关和 DNS（替换为你实际的环境）

nmcli connection modify ens33 ipv4.address 192.168.16.103/24
nmcli connection modify ens33 ipv4.gateway 192.168.16.1
nmcli connection modify ens33 ipv4.dns 223.5.5.5
nmcli connection modify ens33 ipv4.method manual

重新加载网卡配置生效
nmcli connection down ens33 && nmcli connection up ens33

### 1.2 关闭防火墙和 SELinux（实验环境）

关闭防火墙并取消开机自启（永久关闭，防止重启后复现拦截）
systemctl stop firewalld
systemctl disable firewalld 
setenforce 0
1. 关闭防火墙
2. 禁止开启自启
3. 关闭Linux 内核里的**强制访问控制机制**

临时关闭 SELinux（如果你希望永久关闭，需修改 /etc/selinux/config 文件里的 SELINUX=disabled）

> 💡 **踩坑点**：如果不关防火墙，网页前端 80/8080 端口打不开，Zabbix 的 10051、10050 端口会被拦截，导致无法监控。
## 2. 安装相关软件（搭建土壤）
Zabbix 的运行需要 LNMP（Linux + Nginx + MySQL + PHP）环境支持。

### 2.1 安装基础软件

yum install vim tar net-tools wget -y

- **解析**：`vim` 文本编辑器、`tar` 压缩解压、`net-tools` 包含 `netstat` 等网络命令、`wget` 下载工具。
- 
### 2.2 安装 LNMP 核心软件

_(⚠️ 笔记补充：原文档漏了 `php-fpm` 和 `php-mysqlnd`，Zabbix 7.2 强依赖这两个包)_

yum install nginx -y                 # 显示 Zabbix 管理界面
yum install mysql-server -y          # 存储 Zabbix 数据
yum install php php-fpm php-mysqlnd -y # 处理页面逻辑和连接数据库

| 部分            | 角色                        |
| ------------- | ------------------------- |
| 浏览器           | 你看到界面的地方                  |
| Nginx         | Web 服务器，负责接收 HTTP 请求、返回页面 |
| php-fpm       | 执行 PHP 代码的进程              |
| Zabbix Web 前端 | PHP 写的管理界面程序              |
| MySQL         | 存 Zabbix 配置、监控数据、用户等      |
| Zabbix Server | 真正干监控的后端引擎，采集数据、触发告警      |
| Zabbix Agent  | 被监控主机上的采集代理               |

### 2.3 安装 Zabbix 官方 Yum 源

rpm -Uvh https://repo.zabbix.com/zabbix/7.2/release/centos/9/noarch/zabbix-release-latest-7.2.el9.noarch.rpm
刷新 yum 缓存（可做可不做）
yum clean all
yum makecache

### 2.4 安装 Zabbix 相关软件

yum install zabbix-server-mysql zabbix-web-mysql zabbix-nginx-conf zabbix-sql-scripts zabbix-selinux-policy zabbix-agent -y

## 3. 数据库配置（初始化与导表）

### 3.1 启动数据库并设置密码

systemctl start mysqld
systemctl enable mysqld

_(实验环境可跳过安全初始化 `mysql_secure_installation`，默认 root 密码为空)_

### 3.2 创建 Zabbix 数据库和用户（逐行解析）

登录数据库：

mysql -u root -p

_(直接回车进入，因为默认密码为空)_

执行以下 SQL 语句（**强烈建议复制粘贴，避免手动敲错**）：

create database zabbix character set utf8mb4 collate utf8mb4_bin;
create user 'zabbix'@'%' identified by '2wsx#EDC';
grant all privileges on zabbix.* to 'zabbix'@'%';
set global log_bin_trust_function_creators = 1;

quit;

> 🔍 **逐行解析与踩坑点**：
> 
> - `create database ... character set utf8mb4 ...`：创建名为 `zabbix` 的数据库。**踩坑点**：务必使用 `utf8mb4` 和 `utf8mb4_bin`，这是 Zabbix 官方要求的字符集（支持中文、韩文甚至 Emoji）。如果用默认的 latin1，后续监控中文主机名或告警时会乱码报错。
>     
> - `create user 'zabbix'@'%' ...`：创建名为 `zabbix` 的用户，密码为 `2wsx#EDC`。**解析**：`%` 代表允许从任何 IP 远程登录，这样以后你部署在其他机器上的 Agent 也能连过来。
>     
> - `grant all privileges on zabbix.* to ...`：把 `zabbix` 库的所有权限授权给该用户。
>     
> - `set global log_bin_trust_function_creators = 1;`：**核心踩坑点！** 因为 MySQL 开启了 binlog，导入 Zabbix 数据时会创建存储过程和函数，如果不设置为 1，数据库会报错拒绝创建（`ERROR 1418`）。
>     
> - `quit;`：退出数据库。
>     

### 3.3 导入 Zabbix 数据库表项

bash

zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix

> 🔍 **命令解析**：
> 
> - `zcat`：读取压缩包 `.gz` 里的内容，相当于解压并读出里面的一大堆建表 SQL 命令。
>     
> - `|`（管道符）：把 `zcat` 读出来的命令，交给后面的 `mysql` 程序去执行。
>     
> - `--default-character-set=utf8mb4`：强制导入过程使用 utf8mb4，防止中文乱码。
>     
> - `-uzabbix -p zabbix`：以 `zabbix` 用户身份（会提示输入密码 `2wsx#EDC`），操作 `zabbix` 数据库。
>     
> - _(⚠️ 注意：导入过程需要几十秒，期间没有任何输出，不报错就是成功。)_
>     

**导入完成后，恢复权限（极其重要）：**


mysql -u root -p
set global log_bin_trust_function_creators = 0;
quit;

> 💡 **踩坑点**：导完数据后必须把 `log_bin_trust_function_creators` 改回 `0`，防止数据库被恶意提权，这是最佳安全实践。

## 4. Nginx 配置

### 4.1 修改配置文件


vim /etc/nginx/conf.d/zabbix.conf

![[Pasted image 20260915103215.png]]

找到 `server {` 里的 `listen 8080;` 和 `server_name example.com;`，**把前面的 `#` 注释删掉**，改成你自己的 IP 或域名（例如 `server_name 192.168.16.103;`），然后保存退出（`:wq`）。

_(⚠️ 笔记解析：Zabbix 官方默认监听 8080，因为 80 端口常被系统默认页面占用。你也可以改成 80，但要注意处理默认页面)_

### 4.2 启动服务并检查端口


systemctl restart nginx
netstat -nltp | grep 8080

![[Pasted image 20260915103236.png]]

如果看到 `tcp 0 0 0.0.0.0:8080 ... LISTEN` 类似字样，说明 Nginx 成功监听。

> 💡 **踩坑点**：如果此时浏览器还是打不开，大概率是浏览器缓存了之前的默认页面，按 `Ctrl + F5` 强制刷新。

---

## 5. Zabbix Server 配置与避坑

### 5.1 修改配置文件

bash

vim /etc/zabbix/zabbix_server.conf

![[Pasted image 20260915103313.png]]

找到 `DBPassword=` 这一行，改成你设置的密码：


DBPassword=2wsx#EDC

_(⚠️ 确保前面的 `#` 号已经删掉，否则 Zabbix Server 启动时会报 `Access denied for user 'zabbix'@'localhost' (using password: NO)`)_

### 5.2 启动相关服务

bash

systemctl restart zabbix-server zabbix-agent nginx php-fpm
systemctl enable zabbix-server zabbix-agent nginx php-fpm

### 5.3 openEuler 专属踩坑点（net-snmp-libs 兼容性报错）

在 openEuler 系统中，如果直接重启 Zabbix Server 会报错，大概率是自带的 `net-snmp-libs` 版本与 Zabbix 存在兼容性问题。

**解决方案：**

#1. 移除有问题的 net-snmp-libs
yum remove net-snmp-libs -y
#2. 增加阿里云的 CentOS Stream 9 源，安装适配版本
vim /etc/yum.repos.d/aliyun.repo

按 `i` 插入，粘贴以下内容：

[aliyun]
name=aliyun
baseurl=https://mirrors.aliyun.com/centos-stream/9-stream/AppStream/x86_64/os
gpgcheck=0
enabled=1

按 `Esc`，输入 `:wq` 保存。

#3. 查找并安装适配的 net-snmp 版本
yum provides net-snmp-libs*
yum install net-snmp-libs-1:5.9.1-16.el9.x86_64 -y

#4. 重新安装 zabbix-server-mysql 补全依赖
yum install zabbix-server-mysql -y

#5. 再次启动服务
systemctl restart zabbix-server zabbix-agent nginx php-fpm

### 5.4 验证服务状态

bash

systemctl status zabbix-server

看到绿色的 `active (running)` 即可。此时打开浏览器访问 `http://你的IP:8080`，就能看到 Zabbix 的欢迎安装界面了（这也是你文档第 8 页开始的网页配置环节）。

## 6. Web 前端初始化配置（收尾之战）

服务端启动后，打开浏览器访问 `http://你的服务器IP:8080`（如果改了端口则用80），进入 Zabbix 的网页安装向导。

### 6.1 语言与时区选择

- **Welcome 界面**：下拉选择 `中文(zh_CN)`，点击“下一步”。
    
    - _(💡 踩坑点：如果下拉框里没有中文选项，需要在服务器终端执行 `yum install glibc-common glibc-langpack-zh -y`，然后重启 php-fpm 和 nginx)。_
        ![[Pasted image 20260915103740.png]]
- **检查必要条件（Check of pre-requisites）**：确保所有项前面都是绿色的 `OK`。
    
    - _(💡 踩坑点：如果出现红色的 Fail，通常是 PHP 的某些扩展没装全，或者 PHP 时区未设置。根据提示补装包即可)。_
        
![[Pasted image 20260915103835.png]]
### 6.2 配置数据库连接

这是连接前端和后端数据库的关键环节：
![[Pasted image 20260915103850.png]]

- **数据库类型**：`MySQL`
    
- **数据库主机**：`localhost`（如果数据库在本机，填 localhost 走本地 Socket 速度最快；也可以填真实 IP `192.168.16.103`）
    
- **数据库端口**：`0`（填 0 表示使用默认端口或 Socket 连接，无需手动填 3306）
    
- **数据库名称**：`zabbix`
    
- **用户**：`zabbix`
    
- **密码**：`2wsx#EDC`
    
- **数据库 TLS 加密**：视情况勾选。如果数据库和 Zabbix 都在同一台机器，不勾选也可以；如果分属不同机器，建议勾选并配置证书（实验环境本机的话，保持默认或不勾选即可）。
    
- **验证数据库证书**：不勾选。
    

### 6.3 设置（Settings）
![[Pasted image 20260915103940.png]]
- **Zabbix 主机名称**：默认是 `Zabbix server`，这里可以改成你喜欢的名字（比如 `第一台zabbix`），这个名字会显示在页面左上角。
    
- **默认时区**：**强烈建议选择 `Asia/Shanghai` 或 `Asia/Beijing`**。
    
    - _(💡 踩坑点：千万不要用默认的 UTC 时间，否则你页面上的图表时间会比你的北京时间慢 8 个小时，排查问题时会把自己逼疯！)_
        
- **默认主题**：选默认的“蓝”即可。
    

### 6.4 安装前汇总与登录


![[Pasted image 20260915104005.png]]


![[Pasted image 20260915104014.png]]

一路点击“下一步”，最后点击“完成”。此时会跳出登录界面：

![[Pasted image 20260915104049.png]]
- **用户名**：`Admin`（**注意 A 必须大写**）
    
- **密码**：`zabbix`
    
- **登录成功后第一件事**：去右上角用户设置里，**立刻修改默认密码**，防止被恶意扫描。
    ![[Pasted image 20260915104103.png]]

---

## 7. 配置被监控主机（安插卧底）

现在的你已经是“指挥官”了，接下来去另一台机器（或者本机）上安插“卧底”（Zabbix Agent）。

### 7.1 在被监控端安装 Agent

_(假设被监控机是一台全新的 openEuler/CentOS 9 机器，IP 为 `192.168.16.104`)_

bash

#1. 安装官方源
rpm -Uvh https://repo.zabbix.com/zabbix/7.2/release/centos/9/noarch/zabbix-release-latest-7.2.el9.noarch.rpm
yum clean all
#2. 安装 agent 程序
yum install zabbix-agent -y
#3. 编辑 agent 配置文件
vim /etc/zabbix/zabbix_agentd.conf

### 7.2 修改 Agent 配置文件（逐行解析）

在 `zabbix_agentd.conf` 中，找到以下两行并进行修改：

ini

\#告诉 Agent：只允许哪个 Zabbix Server 来连我？（填 Server 的 IP）
Server=192.168.16.103

\#告诉Agent：主动把数据发给谁？
ServerActive=192.168.16.103
 
 \#告诉 Agent：我叫什么名字？（如果监控本机，可保持默认 Zabbix server；如果监控新机器，改成新机器的名字，比如 192.168.16.104）
Hostname=192.168.16.104

修改两项参数

Server=172.22.100.3    zabbix服务器的ip地址

Hostname=172.22.100.2 自己的ip地址（被监控的对象）

> 🔍 **深度解析与踩坑点**：
> 
> - `Server` 和 `ServerActive` 必须填 Zabbix Server 的 IP（即 `192.168.16.103`）。
>     
> - `Hostname` 极其重要！**它的名字必须和网页端添加主机时的“主机名称”一模一样**，差一个字母就会导致图标变红。
>     

**启动服务并放行网络：**

bash

systemctl start zabbix-agent
systemctl enable zabbix-agent

\#关闭防火墙和 SELinux（新手防坑最快手段）
systemctl stop firewalld
setenforce 0

> 💡 **踩坑点**：如果你不关防火墙，Zabbix Server 发出的 TCP 10050 端口探测包会被拦截，网页端会一直显示红色的 ZBX 图标。

### 7.3 在 Web 端添加主机

回到指挥官（Web 页面）：

1. 左侧菜单进入 **数据采集 (Data collection)** -> **主机 (Hosts)**，点击右上角 **创建主机**。
    ![[Pasted image 20260915104915.png]]
2. **主机名称**：填 `192.168.16.104`（与 Agent 配置文件里的 `Hostname` 完全一致！）。
    ![[Pasted image 20260915104921.png]]
3. **可见的名称**：可以填 `测试服务器1`（随便起，给自己看的）。
    
4. **主机群组**：选择 `Linux servers`。
    
5. **接口**：点击“添加”，类型选 `Agent`，IP 地址填 `192.168.16.104`，端口保持 `10050`。
    
6. **模板**：点击蓝色的“选择”按钮。
    
    - _(💡 踩坑点：如果搜索不到模板，说明 Zabbix 自带的模板库没有导入，或者网络问题没下载。解决办法是手动去网上找 `template_os_linux_active.yaml` 文件，在 `数据采集 -> 模板 -> 导入` 里上传进系统。)_
        
    - _(✓ 实战经验：如果导入麻烦，也可以手动创建一个新模板，添加核心监控项 `agent.ping`、`system.cpu.load[percpu,avg1]` 等应急。)_
        
7. 勾选 `Linux by Zabbix agent active`，点击“选择”。
    

### 7.4 验证监控状态

1. 点击页面底部的 **添加**。
    
2. 回到主机列表页。
    
3. **耐心等待 1-2 分钟**。
    ![[Pasted image 20260915105052.png]]
4. 观察 `ZBX` 图标：
    
    - **绿色**：成功连通！（此时去 `监测 -> 最新数据` 就能看到 CPU、内存曲线了。）
        
    - **红色**：通讯失败！（排错四步曲：1. 检查网络能否 ping 通；2. 检查被监控机防火墙是否关闭；3. 检查 Server 和 Hostname 拼写是否一致；4. 检查 Agent 服务是否运行 `systemctl status zabbix-agent`。）
[[zabbix 监控的核心架构]]

# Zabbix 部署实战笔记（第八步：配置邮件告警）

## 8.0 邮件告警的整体逻辑（核心思维导图）

很多新手配置邮件告警时容易晕头转向，是因为没搞清楚 Zabbix 发一封邮件需要经过几个独立的环节。我们把它比作**“寄一封挂号信”**，一共需要四个要素：

1. **邮局（媒介类型 Media Type）**：你用什么渠道寄信？（例如：163邮箱、企业微信、钉钉）。你需要配置 SMTP 服务器地址、端口、发件账号和授权码。
    
2. **收件人（用户媒介 User Media）**：这封信寄给谁？（在 Admin 用户的“报警媒介”标签页里，绑定他的收件邮箱地址和接收级别）。
    
3. **寄信条件（动作 Action）**：遇到什么事才寄信？（是 CPU 大于 90% 寄？还是机器直接宕机才寄？需要在“动作”里配置触发条件）。
    
4. **信件内容（消息模板 Message Template）**：信纸上写什么？（是写“出事了”，还是写清楚“主机名: sky1，故障: CPU 过高，时间: 10:00”）。
    

**一句话总结链条**：触发器产生问题 -> 匹配到**动作** -> 动作调用绑定的**用户** -> 用户使用配置好的**媒介类型** -> 按照**消息模板**发信。

---

## 8.1 准备工作：配置发件邮箱（以163为例）

1. 登录 163 邮箱网页版。
    
2. 进入 **设置 -> POP3/SMTP/IMAP**。
    
3. 开启 **SMTP 服务**（需手机发短信验证）。
    
4. **获取 16 位授权码**（复制保存，它只显示一次！）。
    
    > 🚨 **踩坑点 1**：Zabbix 里配置的密码，**必须是授权码，绝对不能填邮箱登录密码！**
    

---

## 8.2 核心配置（在 Zabbix Web 端操作）

### 第一步：配置“邮局”（新增媒介类型）
![[Pasted image 20260915110504.png]]
_路径：告警 (Alerts) -> 媒介类型 (Media types) -> 创建媒介类型_

![[Pasted image 20260915110528.png]]
- **名称**：`测试邮件告警`
    
- **类型**：`电子邮件`
    
- **SMTP服务器**：`smtp.163.com`
    
- **SMTP服务器端口**：`465`
    
- **电子邮件**：`你的邮箱@163.com`（发件人）
    
- **SMTP HELO**：`163.com`
    
- **安全连接**：选择 `SSL/TLS`
    
- **认证**：选择 `用户名和密码` -> 用户名填完整邮箱地址，密码填刚才的**授权码**。
    
- **消息格式**：`HTML`
    

> 🚨 **踩坑点 2（实战血泪教训）**：**必须点击顶部的“消息模板”标签页！**  
> 对于自定义媒介类型，如果不添加消息模板，Zabbix 尝试发信时会直接报错 `No message defined for media type.`。  
> **解决办法**：
> 
> - 点击“添加” -> 消息类型选 `问题` -> 主题填 `故障: {EVENT.NAME}` -> 消息填 `主机: {HOST.NAME} 发生故障，请尽快处理！`
>     
> - 再次点击“添加” -> 消息类型选 `问题恢复` -> 主题填 `恢复: {EVENT.NAME}` -> 消息填 `故障已恢复。`
>     

### 第二步：配置“收件人”（给用户绑定媒介）
![[Pasted image 20260915110704.png]]
_路径：用户 (Users) -> 用户 -> 点击 Admin -> 报警媒介 (Media)_
![[Pasted image 20260915110708.png]]
- 点击“添加”：
    
    - **类型**：选择刚才创建的 `测试邮件告警`
        
    - **收件人**：填 `你的邮箱@163.com`（这里填接收告警的邮箱）
        
    - **当启用时**：选择 `一直`
        
    - **如果严重性**：全部勾选（警告、严重、灾难等）
        
- 点击“添加”，然后**务必点击页面底部的“更新”按钮**。
    

### 第三步：配置“寄信条件”（配置动作）

_路径：告警 (Alerts) -> 动作 (Actions) -> 触发器动作 (Trigger actions)_
![[Pasted image 20260915110559.png]]
![[Pasted image 20260915110609.png]]
- 点击右上角 **创建动作**。
    
- **名称**：`发送故障邮件`
    
- **条件**：可保留默认（比如 `触发器严重性 >= 警告`）。
    
- 点击顶部的 **操作 (Operations)** 标签页：
    
    - 点击“添加”：
        
        - **发送给用户**：选择 `Admin`
            
        - **仅送到**：**务必选择“仅送到”，并在下拉框中选中 `测试邮件告警`！**
            ![[Pasted image 20260915111116.png]]
        
        > 🚨 **踩坑点 3**：如果你选了“全部可用”，Zabbix 会尝试发送到该用户绑定的所有渠道。如果用户没绑全，或者绑了其他有问题的渠道，会导致整个动作失败。
        
    - 点击底部的“添加”，保存动作。
        

---

## 8.3 命令行测试发信（可选，作为Linux拓展知识）

_(对应你文档第14-15页的内容)_

如果你也想在 Linux 终端测试一下发信，可以安装 `s-nail`（Rocky/CentOS 9 已取代 `mailx`）：

bash

sudo yum install s-nail -y
sudo vim /etc/s-nail.rc

在文件末尾添加以下内容：

ini

set from="你的邮箱@163.com"
set mta=smtps://smtp.163.com:465
set smtp-auth-user="你的邮箱@163.com"
set smtp-auth-password="16位授权码"
set smtp-auth=login
set ssl-verify=ignore

> 🔍 **深度解析**：`set mta=smtps://...` 这行极其重要。如果写成 `set smtp=smtp.163.com:465`，系统可能会报错 `Cannot start /usr/sbin/sendmail: executable not found`，因为 `mta` 参数会强制它直接连远程 SMTP，不找本地服务。  
> 测试命令：`echo "测试内容" | mail -s "测试主题" 你的邮箱@163.com`

---

## 8.4 验证与排错（最关键的环节）

1. **通道测试**：去 `告警 -> 媒介类型 -> 找到你的邮件 -> 点击右侧“测试”`。收到测试邮件，说明**邮局工作正常**。
    
2. **制造故障**：去被监控机终端执行 `sudo systemctl stop zabbix-agent`（拔掉卧底网线）。
    
3. **观察日志**：等 2-3 分钟，去 `报表 -> 动作日志`。
    
    - 如果状态是 **`已发送 (Sent)`**，大功告成！
        
    - 如果状态是 **`已失败 (Failed)`**，点击右侧红色的感叹号 `!` 查看原因。
        
        - `Authentication failed` = 授权码错了。
            
        - `No message defined...` = 消息模板没配（参考踩坑点 2）。
            
        - `User does not have any media` = Admin 用户没绑定邮箱。
            
4. **查收邮件**：去 163 邮箱收信。**务必检查垃圾箱！** Zabbix 的自动邮件极容易被网易拦截。如果收到，记得标记为“非垃圾邮件”。
    

---

### 📝 写给你的复盘小结

你在实战中踩过的**“No message defined”**和**“媒介测试通过但动作日志失败”**这两个坑，是 Zabbix 新手最经典的噩梦。通过这次整理，以后无论你换钉钉、企业微信还是短信网关，逻辑是完全相通的：**先建渠道（媒介），再绑用户，最后配动作。**

整理完第八步，你的整套 Zabbix 监控体系（环境搭建 + 数据库 + 前端 + Agent + 告警）就彻底闭环了！需要我帮你整理第九步（常见问题与SNMP交换机监控）吗？

# Zabbix 部署实战笔记（第九步：常见问题与扩展监控）

## 9.1 常见问题处理（排坑指南）

### 🔧 问题一：Web 界面没有中文选项

Zabbix 支持中文，但如果系统镜像是最小化安装，可能缺少中文字符集包。  
在服务器终端执行：

bash

yum install glibc-common glibc-langpack-zh -y
# 生成中文环境
localedef -c -f UTF-8 -i zh_CN zh_CN.UTF-8

_(💡 实战提示：安装完重启一下 php-fpm 和 nginx 即可生效)_

### 🔧 问题二：图形化字体异常（中文显示为方块/乱码）

Zabbix 默认使用的字体 `DejaVuSans.ttf` 不包含中文字形，导致图表中的中文字符无法渲染。  
**解决办法：**

1. 在你自己电脑（Windows）的 `C:\Windows\Fonts` 文件夹里，找一款中文字体（比如 `msyh.ttc` 微软雅黑 或 `simhei.ttf` 黑体）。
    
2. 通过 Xftp / WinSCP 等工具，把字体文件上传到 Zabbix 服务器的 `/usr/share/fonts/dejavu-sans-fonts/` 目录下。
    
3. 备份原字体，并将中文字体重命名为 `DejaVuSans.ttf` 覆盖原文件：
    

bash

cd /usr/share/fonts/dejavu-sans-fonts/
mv DejaVuSans.ttf DejaVuSans.ttf.bak
mv 你的中文字体.ttf DejaVuSans.ttf

4. 刷新浏览器，中文即可正常显示。
    

### 🔧 问题三：防火墙与 SELinux 的残留拦截（重启后失效）

之前在部署初期，我们使用的 `systemctl stop firewalld` 和 `setenforce 0` 是**临时生效**的。一旦服务器重启，防火墙和 SELinux 会自动恢复，导致 Zabbix 页面打不开、Agent 失联。  
**永久关闭的办法（实验环境专属）：**

bash

#1. 彻底禁用防火墙
systemctl stop firewalld
systemctl disable firewalld

#2. 永久禁用 SELinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config

\#然后临时生效一次
setenforce 0

---

## 9.2 扩展监控场景（纳管非 Linux 设备）

Zabbix 的强大之处在于它不仅能监控 Linux，还能通过不同协议监控 Windows 和网络设备。

### 🌐 场景一：监控 Windows 服务器

Windows 机器无法直接跑 Linux 命令，但官方提供了 Windows 版的 Agent 安装包（MSI）。

1. 去 Zabbix 官网下载对应的 `zabbix_agent-7.2.x-windows-amd64-openssl.msi`。
    [Download Zabbix agents](https://www.zabbix.com/download_agents)
    ![[Pasted image 20260915123343.png]]
2. 双击安装，在安装向导中填入：
    ![[Pasted image 20260915123404.png]]
    - **Zabbix Server IP**：你的 Zabbix 服务器 IP。
        
    - **Hostname**：这台 Windows 机器的名字。
        
3. 安装完成后，Windows 服务列表中会自动启动 `Zabbix Agent` 服务。
    
4. 在 Web 端添加主机时，关联模板选 `Windows by Zabbix agent active` 即可。
    ![[Pasted image 20260915131121.png]]

### 🔀 场景二：监控网络设备（交换机/路由器）

交换机、防火墙通常是嵌入式系统，无法安装 Agent，但基本都支持 **SNMP 协议（简单网络管理协议）**。Zabbix 通过 SNMP 主动去“拉取”交换机的数据。

**第一步：在交换机上开启 SNMP 并配置团体名（对应文档第26页）**

bash

# 以华为交换机为例（配置命令解析）：
system-view
snmp-agent sys-info version v2c                     # 开启SNMP v2c版本
snmp-agent community read 2wsx#EDC                   # 设置只读团体名（相当于密码）
snmp-agent trap enable                               # 开启Trap主动上报功能
snmp-agent target-host host-name NMS1 trap address udp-domain 10.1.1.50 udp-port 162 params securityname 2wsx#EDC v2c
commit

> 🔍 **命令解析与踩坑点**：
> 
> - `snmp-agent community read`：设置团体名，Zabbix 端要用这个字符串认证。
>     
> - `snmp-agent target-host ... address udp-domain 10.1.1.50 udp-port 162`：告诉交换机，如果发生故障（Trap），主动发给 IP 为 `10.1.1.50` 的 Zabbix 服务器，端口是 UDP `162`（注意，不是 10051，SNMP Trap 的默认端口是 162）。
>     
> - 需要在 Zabbix 服务器的防火墙上放行 UDP 162 端口。
>     

**第二步：在 Zabbix 服务器上安装 SNMP 工具**

bash

yum install net-snmp net-snmp-utils -y

**第三步：在 Web 端添加交换机**

1. 创建主机 -> 接口选 `SNMP` -> 填交换机 IP -> 端口 `161`。
    
2. 模板搜索 `SNMP`，关联对应的交换机模板（如 `Network Generic Device by SNMP` 或华为/思科专属模板）。
    
3. 配置宏（Macros）：在主机设置里，添加宏 `{$SNMP_COMMUNITY}`，值填 `2wsx#EDC`（即交换机上设的团体名）。
    

---

## 9.3 数据库字符集与初始化复盘（深化理解）

_(对应你笔记文档最后一页的疑问)_

**为什么建库一定要用 `utf8mb4`？**

- `latin1`：只支持英文，遇到中文监控项名称会直接乱码。
    
- `utf8`（MySQL 里的伪 UTF-8）：最多支持 3 字节，支持中韩文，但**不支持 Emoji**。
    
- `utf8mb4`：真正的 UTF-8，最多支持 4 字节，支持世界上所有文字和 Emoji。Zabbix 官方强烈建议使用它。
    

**数据库初始化回顾：**

sql

create database zabbix character set utf8mb4 collate utf8mb4_bin;
create user 'zabbix'@'%' identified by '2wsx#EDC';
grant all privileges on zabbix.* to 'zabbix'@'%';
set global log_bin_trust_function_creators = 1;
quit;

- `collate utf8mb4_bin`：指定排序规则。
    
- `set global log_bin_trust_function_creators = 1`：为了给 Zabbix 创建存储过程放权。
    
- `quit;`：完成退出。
    

> 🔍 **总结建议**：  
> 在运维工作中，命令死记硬背毫无意义。遇到问题，懂逻辑（比如“数据库存数据，前端展示，Agent收集”），知道去哪查日志（`/var/log/zabbix/zabbix_server.log`），才是真正有价值的能力。你的这次真实踩坑记录，已经比只看视频的人强太多了。

---

### 🎉 全篇结语（写给你自己的话）

至此，从系统初始化、LNMP搭建、数据库配置、Zabbix Server安装、Web初始化、Agent纳管、邮件告警到扩展监控（SNMP），一套企业级监控系统的完整实施流程已被你彻底打通。踏踏实实按这份笔记走一遍，这绝对能成为你日后运维工作或个人简历中实打实的实战亮点。加油！




# zabbix 监控的核心架构
[[zabbix 监控的核心架构]]




### 其他

![[Pasted image 20260914235838.png]]



![[Pasted image 20260914213341.png]]