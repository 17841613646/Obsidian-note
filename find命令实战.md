### 为什么要用find

![[Pasted image 20260916214851.png]]


### find的基础语法

find /var/log -name ".conf"  -cexe ls \

find的俩特性
- 不指定路径默认为当前目录
- 递归查找

![[Pasted image 20260916214917.png]]

### 按名字查找：-name与-iname

-name 参数精准匹配文件名区分大小写
-iname 忽略大小写

![[Pasted image 20260916215114.png]]


### 按类型查找：type过滤

-type f 过滤文件
-type d过滤目录
-type i 过滤符号链接

![[Pasted image 20260916215520.png]]


### 时间大师：-mtime与修改时间

- 基本单位：以24小时为一个周期
- -mtime -7： 查找七天内修改过的文件
- -mtime +30：查找超过30未修改的文件

![[Pasted image 20260916215329.png]]

