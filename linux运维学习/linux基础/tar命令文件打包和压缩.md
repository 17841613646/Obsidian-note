### 文件打包和压缩的区别

打包不瘦身，压缩瘦身

![[Pasted image 20260916230707.png]]

### tar命令的基础语法

tar -zcvf test.tar a/b
- -c：打包
- -x：解压
- -t：列出压缩包里的内容
- -v：显示详细处理过程
- -f：指定文件
-f参数必须在参数末尾紧跟文件名
![[Pasted image 20260916230758.png]]

### 实战：创建打包文件 -cvf


![[Pasted image 20260916231125.png]]

### Gzip压缩(-zcvf)

-z调用Gzip压缩算法
命名规则.tar.gz
![[Pasted image 20260916231208.png]]

### 极限压缩：Bzip2格式（-jcvf）

![[Pasted image 20260916231315.png]]

### 压缩算法区别

![[Pasted image 20260916231405.png]]

### 解压缩（-xvf）

tar -xvf test.tar.gz -C test01
-x解压
自动识别压缩算法
解压前先预览 -tvf
![[Pasted image 20260916231423.png]]

### 指定解压路径： -C参数

tar -zxvf test.tar.gz -C /tmp

![[Pasted image 20260916231607.png]]


![[Pasted image 20260916231712.png]]



### tar实用操作

打包前进入目录使用相对路径压缩

![[Pasted image 20260916231741.png]]![[Pasted image 20260916231817.png]]


### 总结

![[Pasted image 20260916231832.png]]