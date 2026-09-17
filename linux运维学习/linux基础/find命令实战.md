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

### 逻辑组合：与 、或、非

-a 同时满足
-o 满足两者其一即可
！/ -not 逻辑非

![[Pasted image 20260916215656.png]]


![[Pasted image 20260916215725.png]]

### -exec与动作处理

-exec命令 {} \;
find找到的文件放在{}中执行前面的命令  然后结束\

- {} 占位符
- \ \;结束符
-exec风险高 可以用-ok 代替

![[Pasted image 20260916220001.png]]

![[Pasted image 20260916220216.png]]

### 综合实战

![[Pasted image 20260916220253.png]]

### 总结

![[Pasted image 20260916220325.png]]