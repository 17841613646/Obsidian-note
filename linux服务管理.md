systemd是linux默认初始化系统

# 1.启动，停止，重启服务
### 1.1启动服务
systemctl start 服务名
### 1.2停止服务
systemctl stop 服务器
### 1.3重启服务
systemctl restart 服务名

### 1.4查看服务状态
systemctl status nginx

### 1.5重新加载服务
systemctl reload 服务名

# 2设置服务开机自启
设定服务自启动
systemctl enable 服务名

关闭服务自启动
systemctl disable 服务名

查看是否自启

systemctl is-enabled 服务名 

### 3.日志查看

journalctl -u nginx
查看nginx的服务日志

journalctl -since "2025-02-10" --until "2025-4-10"
查看特定时间段日志

journalctl -f
实时刷新日志

journalctl --vacuum-time=2weeks
清理两周前日志


### 创建自定义服务单元
服务单元地址
/etc/systemd/system/

服务单元分为
\[Unit]\[service]\[Install]


创建一个自定义服务步骤
1. 创建一个可执行文件
2. 进入cd  /etc/systemd/system/
3. 创建一个服务文件myapp.service
4. 进入文件编辑 vim myapp.service
5. 写入
\[Unit]
服务信息
Description=My Custom Application

\[Service]
如何启动服务
ExecStart=/usr/bin/qiubai
是否自启动
Restart=always

\[install]
多用户启动
wantedBy=multi.user.target
6. systemctl  deamon-reload


## 安装nginx
yum -y insatll nginx
-y：对所有的提问都回答“yes”；