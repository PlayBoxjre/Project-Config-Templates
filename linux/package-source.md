# apt源 

http://wiki.ubuntu.org.cn/

```
deb http://cn.archive.ubuntu.com/ubuntu/ xenial main restricted universe multiverse
deb http://cn.archive.ubuntu.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://cn.archive.ubuntu.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://cn.archive.ubuntu.com/ubuntu/ xenial-backports main restricted universe multiverse
##測試版源
deb http://cn.archive.ubuntu.com/ubuntu/ xenial-proposed main restricted universe multiverse
# 源碼
deb-src http://cn.archive.ubuntu.com/ubuntu/ xenial main restricted universe multiverse
deb-src http://cn.archive.ubuntu.com/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://cn.archive.ubuntu.com/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://cn.archive.ubuntu.com/ubuntu/ xenial-backports main restricted universe multiverse
##測試版源
deb-src http://cn.archive.ubuntu.com/ubuntu/ xenial-proposed main restricted universe multiverse
# Canonical 合作夥伴和附加
deb http://archive.canonical.com/ubuntu/ xenial partner
deb http://extras.ubuntu.com/ubuntu/ xenial main
```



# 更改pip源至国内镜像，显著提升下载速度

**pip国内的一些镜像**

  阿里云 <http://mirrors.aliyun.com/pypi/simple/> 
  中国科技大学 <https://pypi.mirrors.ustc.edu.cn/simple/> 
  豆瓣(douban) <http://pypi.douban.com/simple/> 
  清华大学 <https://pypi.tuna.tsinghua.edu.cn/simple/> 
  中国科学技术大学 <http://pypi.mirrors.ustc.edu.cn/simple/>

**修改源方法：**

**临时使用：** 
可以在使用pip的时候在后面加上-i参数，指定pip源 
eg: pip install scrapy -i <https://pypi.tuna.tsinghua.edu.cn/simple>

**永久修改：** 
**linux:** 
修改 ~/.pip/pip.conf (没有就创建一个)， 内容如下：

```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple12
```

**windows:** 
直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件pip.ini，内容如下

```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```