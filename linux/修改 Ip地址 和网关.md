Linux 

## 修改 Ip地址 和网关

**1. Ubuntu Server 16.04修改IP**

```
sudo vi /etc/network/interfaces
```

回显：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet static
        address 192.168.0.88
        netmask 255.255.255.0
        network 192.168.0.0
        broadcast 192.168.0.255
        gateway 192.168.0.1
        # dns-* options are implemented by the resolvconf package, if instatlled
        dns-nameservers 192.168.0.1
        dns-search pcat
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

把address后的ip修改为自己想设定的ip后，保存退出。

之后，重启网络：

```
sudo /etc/init.d/networking restart
```

ps，ubuntu还有别的重启方式（但不一定有效）

```
sudo service network restart
```

desktop版可以这样重启：

```
sudo service network-manager restart
```

 如果只是修改了某个网卡(例如eth0)的信息，也可以通过重启网卡的方式使其修改生效。

```
sudo ifdown eth0
sudo ifup eth0
```

 最后，我在自己机子上试了重启网络或者网卡后，ifconfig里显示的还是旧ip，而且新旧两个ip都可以ping通，只有重启机子后才会显示新的ip。

 

**2.  Ubuntu Server 16.04修改DNS**

```
sudo vi /etc/resolvconf/resolv.conf.d/base
```

修改内容：

```
search localdomain #如果本Server为DNS服务器，可以加上这一句，如果不是，可以不加
nameserver 8.8.8.8 #希望修改成的DNS
nameserver 114.114.114.114 #希望修改成的DNS
```

保存退出，重启网络：

```
sudo /etc/init.d/networking restart
```

查看当前DNS：

```
cat /etc/resolv.conf
```

 

**3.  Ubuntu Server 16.04修改hosts**

查看hostname：

```
cat /etc/hostname
```

记住其hostname，修改hosts：

```
sudo vi /etc/hosts
```

前两行修改为：

```
#第1行默认这个
127.0.0.1       localhost
#第2行为你修改的ip    你刚才查看的hostname
192.168.11.52       pcat
```

保存退出。



## [Ubuntu Server16.04无图形化版，安装后，全命令配置网络](http://blog.csdn.net/wangfengtong/article/details/72780694)

标签： [linux](http://www.csdn.net/tag/linux)[ubuntu](http://www.csdn.net/tag/ubuntu)

2017-05-27 13:39 5007人阅读 [评论](http://blog.csdn.net/wangfengtong/article/details/72780694#comments)(0) [收藏](javascript:void(0);) [举报](http://blog.csdn.net/wangfengtong/article/details/72780694#report)

![img](http://static.blog.csdn.net/images/category_icon.jpg) 分类：

Linux*（9）* ![img](http://static.blog.csdn.net/images/arrow_triangle%20_down.jpg)

版权声明：本文为博主原创文章，未经博主允许不得转载。 <http://blog.csdn.net/wangfengtong/article/details/72780694>

目录[(?)](http://blog.csdn.net/wangfengtong/article/details/72780694#)[[+\]](http://blog.csdn.net/wangfengtong/article/details/72780694#)

### **1安装系统**

su root #从普通用户切换到root用户，根据提示输入root密码

sudo passwd -l root #禁用root账号，如果要启用，输入sudo passwd root再次设置root密码

安装过程中需要设置一个用户名和密码，安装好后用下面的命令来修改，root用户的密码：

sudo passwd root

#### **2配置系统网络**

###### **（1）网络接口配置文件：nano /etc/network/interfaces **

###### #用nano编辑网卡配置文件，当然也可以vim等 /etc/network/interfaces****

(执行此命令，如果提示bash:/etc/network/interfaces:Permission denied    

拒绝访问，没有权限，首先看是不是root用户，是的话执行：chmod 777 /etc/network/interfaces          就修改文件权限了)

 

Ifconfig只显示一个lo，经百度和摸索，是网卡未启动，输入命令：ifconfig -a，显示所有网络接口的信息，无论是否激活。ifconfig显示当前激活的网络接口信息。就可以看见如eth0或enp5s0或ens33或ens192等，然后ifconfig  eth0或enp5s0或ens33或ens192  up即可，再ifconfig就可以看见不是lo一个了(如输入ifconfig -a后还是只有一个lo，网上说缺少驱动，自己再百度吧，我的有就没再查)

 

注释：本地环回接口（不需要改动）

auto lo

iface lo inet loopback

注释：eth0网卡接口的配置

auto enp5s0   #开机自动连接网络(ens0 为网卡名称,ifconfig -a看自己的)

iface enp5s0 inet static#static表示使用固定ip，dhcp表述使用动态ip,若需要动态分配IP的话，static改为dhcp并去掉后面的配置项

address 192.168.3.105    #设置ip地址

netmask 255.255.255.0     #设置子网掩码

(broadcask 114.113.149.127   #广播地址，不知道啥用，暂时没配)

gateway 192.168.3.254        #设置网关 

Vim 就：wq   保存退出(也可以直接在这里配置dns）

##### **（2）域名服务器配置文件： /etc/resolv.conf**

注释：国外的DNS（google提供的），若服务器需要配置VPN进行翻墙的话，需要把国外的DNS放在前面

nameserver 8.8.8.8       #谷歌

nameserver 4.4.4.4  #美国

注释：国内的DNS

nameserver 202.106.0.20  #北京联通

nameserver 159.226.5.65   #北京市中国科学院软件研究所

也可以直接在/etc/network/interfaces 里的最后直接加dns

dns-nameservers 8.8.8.8      #设置DNS,谷歌dns

dns-nameservers 202.106.0.20  北京市联通dns

cat /etc/resolv.conf 查看dns配置

 

配置完查看cat  /etc/network/interfaces，就会显示刚才配置的

 

重启service networking restart

然后ifconfig  就可以看见enp5s0的ip等信息

 

 

用命令ping www.baidu.com看是否配置成功，有返回就成功