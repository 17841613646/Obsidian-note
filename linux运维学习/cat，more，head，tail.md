### 文件内容查看全家桶：从cat到tail



![[Pasted image 20260913182325.png]]

### cat：一次性浏览小文件
cat - n 【文件】
- -n显示行号
![[Pasted image 20260913182902.png]]

### more：基础的分页查看
空格：向下翻一页
回车：向下滚动
不常用无法回滚
![[Pasted image 20260913182945.png]]

### less：强大的交互式分页神器
more vs less
- less支持上下滚动
- 支持搜索
- 支持左右滑动查看长文本
- 可随时退出
- 功能强大
![[Pasted image 20260913183034.png]]


### head & tail：精准切片查看

head -n 5 【文件】
- head：查看文件开头（默认10行）
- tail：查看文件末尾（默认10行）
- -n参数：自定义切片行数

![[Pasted image 20260913183348.png]]

### 运维杀手锏：tail -f实时监控

- tail -f var/log/messages（监控系统消息日志）
- 实时追踪 -f
- 排障神器
- 快捷退出ctrl + c

![[Pasted image 20260913183416.png]]

### 如何选择查看工具

小文件短文件用cat
长文件用less
查看开头或监控日志末尾用head/tail

![[Pasted image 20260913183606.png]]

![[Pasted image 20260913183731.png]]

### 总结

![[Pasted image 20260913183752.png]]