### 什么是进程
进程是软件程序运行的实例
软件程序 死的
进程 活的
PID是软件运行实例的编号

### 程序的状态
1. Running (运行状态)
2. Sleeping (睡眠状态)
3. Zibiz (僵尸状态)
4. Stoped (停止状态)

### 如何查看进程
pa -aux

top实时显示当前进程

pstree 【进程id】显示该进程的子进程树

### 进程的控制

kill 结束进程
kill -2 温柔结束进程
kill -9 强制结束进程

### 不挂断后台运行进程

NOHUP  sh test.sh
### 在后台运行脚本

sh test.sh &