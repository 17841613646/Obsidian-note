![[Pasted image 20260919002804.png]]

### 进程

程序是静态的指令
进程是运行的实例
每个进程都有一个pid

![[Pasted image 20260919002822.png]]

### 静态快照

ps au显示cpu内存占用

![[Pasted image 20260919003136.png]]

### ps与grep联动

ps aux | grep nginx

![[Pasted image 20260919003229.png]]

### top命令

实时监控资源消耗每三秒刷新一次

![[Pasted image 20260919003310.png]]

### top的交互式按键

- p 按cpu使用率排序
- M 按内存使用率排序
- 1 折叠显示cpu所有核心使用率
- k 终止进程
- q 退出程序

![[Pasted image 20260919003614.png]]

### kill
kill \[PID] 温和终止会进行保存
kill -9 \[PID]强制终止不保存

![[Pasted image 20260919003754.png]]

### killall

killall nginx
按名称批量结束进程

![[Pasted image 20260919003910.png]]


### 总结

![[Pasted image 20260919004003.png]]