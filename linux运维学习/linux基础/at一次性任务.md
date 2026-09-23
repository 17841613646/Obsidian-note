
at 命令：一次性任务（零时闹钟）
at 命令用于调度单次执行的任务，而不像 cron 那样是周期性的

案例‌：今晚23点自动关机
echo "shutdown -h now" | at 23:00  		# 管道符输入命令

atq  									
# 查看已设置的临时任务（显示任务编号）

atrm job_number
job_number 是你在 atq 命令输出中看到的任务编号。

比如，要删除任务编号为 3 的任务
atrm 3

举例 
晚上12点执行脚本 test.sh
echo "/home/user/test.sh" | at midnight

具体地指定 今天晚上12点
echo "/home/user/test.sh" | at 00:00 today

执行 明天凌晨 12:00
echo "/home/user/test.sh" | at 00:00 tomorrow
