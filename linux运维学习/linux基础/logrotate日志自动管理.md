`logrotate` 是 Linux 系统中用于自动化管理日志文件的工具，通过轮转、压缩和删除旧日志，防止单个日志文件无限增长占满磁盘[](https://cloud.baidu.com/article/3584812#1)。下面从配置结构到实战示例，帮你系统梳理一遍。



## 📁 配置文件结构

`logrotate` 的配置分为两个层级：

**全局配置文件**：`/etc/logrotate.conf`

定义所有日志的默认轮转策略，例如默认每周轮转、保留4份、启用压缩等。这个文件通常会通过 `include /etc/logrotate.d` 指令，自动加载 `/etc/logrotate.d/` 目录下的所有独立配置文件[](https://cloud.baidu.com/article/3584812#1)。

**自定义配置文件目录**：`/etc/logrotate.d/`

推荐为每个服务单独创建一个配置文件，文件名与业务关联（如 `nginx`、`mysql`、`myapp`），便于分类管理[](https://cn.hostease.com/blog/guides/logrotate-linux-system-log/#content)。

## ⚙️ 核心配置指令详解

一个标准的配置段结构如下：

bash
cd /var/logrotate.d
vim messages
/var/log/messages {
    daily 每天轮转一次
    rotate 7 轮转保存其他
    missingok 文件出错不报错
    notifempty 空文件不轮转
    compress 轮转压缩
    delaycompress 
    create 0640 nginx adm
    sharedscripts
    postrotate
        systemctl reload nginx
    endscript
}

### 轮转频率（三选一）

| 指令          | 作用                              |
| ----------- | ------------------------------- |
| `daily`     | 每天轮转一次                          |
| `weekly`    | 每周轮转一次                          |
| `monthly`   | 每月轮转一次                          |
| `size 100M` | 文件达到指定大小时轮转（可与时间条件同时使用，满足任一即触发） |

### 保留与压缩

|指令|作用|
|---|---|
|`rotate N`|保留最近 N 份归档日志，超出的最旧日志会被删除|
|`compress`|使用 gzip 压缩归档日志|
|`delaycompress`|延迟压缩，下次轮转时才压缩前一次的日志，避免压缩正在写入的文件|

### 文件处理

| 指令                        | 作用                                               |
| ------------------------- | ------------------------------------------------ |
| `missingok`               | 日志文件不存在时不报错                                      |
| `notifempty`              | 空日志文件不进行轮转                                       |
| `create MODE OWNER GROUP` | 轮转后创建新的空日志文件，并指定权限和属主，如 `create 0640 nginx adm`  |
| `dateext`                 | 使用日期作为归档文件的后缀（如 `access.log-20260923`），替代默认的数字编号 |
| `copytruncate`            | 复制原文件后清空，适用于不支持重新打开日志文件的进程（如部分 Java 应用）          |

### 脚本钩子

|指令|作用|
|---|---|
|`sharedscripts`|所有日志文件轮转完成后，`postrotate` 脚本只执行一次|
|`postrotate ... endscript`|轮转后执行的命令，通常用于通知服务重新打开日志文件（如 `kill -USR1` 或 `systemctl reload`）|
|`prerotate ... endscript`|轮转前执行的命令|
## 🧪 测试与调试

**修改配置后，务必先用 `-d` 参数进行干跑测试**：

bash

模拟运行，只显示会执行的操作，不实际修改文件
logrotate -d /etc/logrotate.d/nginx
强制执行一次轮转（忽略时间/大小条件）
logrotate -f /etc/logrotate.d/nginx
详细模式，显示每一步操作
logrotate -v /etc/logrotate.d/nginx

`-d` 模式会打印出 logrotate 将执行的所有操作，但不会真正修改任何文件，是验证配置正确性的最安全方式[](https://cn.hostease.com/blog/guides/logrotate-linux-system-log/#content)。