![[Pasted image 20260919224904.png]]

### crontab 语法格式:核心 “五颗星”

\*   *   *   *   * command
分 时 日 月 周 \[ 要执行的命令]

![[Pasted image 20260919224913.png]]

### 时间字段中的通配符和符号

\*代表每
,代表枚举1,2,3
-代表范围 1-5
/ 代表步长 \*/10每隔10个单位


![[Pasted image 20260919225113.png]]

crontab -e: 编辑当前定时任务
crontab -l:列出所有定时任务
crontab -r:移除所有任务

![[Pasted image 20260919225310.png]]


crontab -e
0 3 * * * /root/backup.sh
为了解决环境缺失问题可以在crontab顶部声明PATH
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
![[Pasted image 20260919232409.png]]

### 排查与日志查看
- 查看crontab服务状态:systemctl status crond
- 日志文件的位置:/var/log/cron
- less /var/log/cron
- 用途用来确认任务是否触发执行
- 常见问题有1权限不够没加（X）2未使用绝对路径调用命令
- 3环境缺失cron默认PATH变量有限
重定向输出
\* * * * *  /path/to/script >> /tmp/cron.log 2>&1

![[Pasted image 20260919232728.png]]

### 总结

![[Pasted image 20260919233150.png]]