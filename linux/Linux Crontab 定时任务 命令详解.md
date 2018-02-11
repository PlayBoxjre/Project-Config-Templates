# Linux Crontab 定时任务 命令详解

在工作中需要数据库在每天零点自动备份所以需要建立一个定时任务．我选择在Linux下使用Crontab来添加定时任务执行shell文件．shell文件有数据库备份命令．

一.  Crontab 介绍

crontab命令的功能是在一定的时间间隔调度一些命令的执行。

二．查看/etc/crontab文件

vim  /etc/crontab

![img](http://www.linuxidc.com/upload/2016_02/160213141588101.png)

三．文件/etc/crontab中每行任务的描述格式如下:

**minute hour day month dayofweek command**

​    minute - 从0到59的整数 
​    hour - 从0到23的整数 
​    day - 从1到31的整数 (必须是指定月份的有效日期)
​    month - 从1到12的整数 (或如Jan或Feb简写的月份)
​    dayofweek - 从0到7的整数，0或7用来描述周日 (或用Sun或Mon简写来表示)
​    command - 需要执行的命令(可用as ls /proc >> /tmp/proc或 执行自定义脚本的命令)

​    root表示以root用户身份来运行
​    run-parts表示后面跟着的是一个文件夹，要执行的是该文件夹下的所有脚本

​    对于以上各语句，星号(*)表示所有可用的值。例如*在指代month时表示每月执行(需要符合其他限制条件)该命令。 
​    整数间的连字号(-)表示整数列，例如1-4意思是整数1,2,3,4

指定数值由逗号分开。如：3,4,6,8表示这四个指定整数。

符号“/”指定步进设置。“/<interger>”表示步进值。如0-59/2定义每两分钟执行一次。步进值也可用星号表示。如*/3用来运行每三个月份运行指定任务。

以“#”开头的为注释行,不会被执行。

如果一个cron任务需要定期而不是按小时,天,周,月来执行,则需要添加/etc/cron.d目录。这个目录下的所有文件和文件/etc/crontab语法相同，查看样例：

\# record the memory usage of the system every monday 
\# at 3:30AM in the file /tmp/meminfo 
30 3 * * mon cat /proc/meminfo >> /tmp/meminfo 
\# run custom scrīpt the first day of every month at 4:10AM 
10 4 1 * * /root/scrīpts/backup.sh

 除了root用户之外的用户可以执行crontab配置计划任务。所有用户定义的crontab存储在目录/var/spool/cron下，任务会以创建者的身份被执行。要以特定用户创建一个crontab，先以该用户登录，执行命令crontab -e，系统会启动在VISUAL或者EDITOR中指定的的编辑软件编辑crontab。文件内容与/etc/crontab格式相同。示例如下：

0 3 * * * /home/dbbackup/db1backup.sh backup
0 4 * * * /home/dbbackup/db2backup.sh backup

表示每天3点执行/home/dbbackup/db1backup.sh backup，4点执行/home/dbbackup/db2backup.sh backup，如果是每五分钟执行一次可改为：

*/5 * * * * /home/dbbackup/db2backup.sh backup

当更改的crontab需要保存时，文件会保存在成如下文件/var/spool/cron/username。文件名会根据用户名而不同。

cron服务会每分钟检查一次/etc/crontab、/etc/cron.d/、/var/spool/cron文件下的变更。如果发现变化，就会下载到存储器中。因此，即使crontab文件改变了，程序也不需要重新启动。推荐自定义的任务使用crontab -e命令添加，退出后用/etc/init.d/crond restart命令重启crond进程，官方文件说不用重启进程，但我遇到不重启无法运行任务的情况。开始不知道/etc/crontab文件中的run-parts是什么意思，直接把命令按照/etc/crontab的格式加上总是无法运行，后来才知道run-parts是指后面跟着的是文件夹。

 四．crontab服务的启动关闭

sbin/service crond start //启动服务
/sbin/service crond stop //关闭服务
/sbin/service crond restart //重启服务
/sbin/service crond reload //重新载入配置

Linux中利用crontab创建计划任务 [http://www.linuxidc.com//Linux/2013-06/86401.htm](http://www.linuxidc.com/Linux/2013-06/86401.htm)

Linux中用crontab例行工作安排 [http://www.linuxidc.com//Linux/2013-06/85441.htm](http://www.linuxidc.com/Linux/2013-06/85441.htm)

Linux crontab不执行问题排查 [http://www.linuxidc.com//Linux/2013-06/85432.htm](http://www.linuxidc.com/Linux/2013-06/85432.htm)

[Ubuntu](http://www.linuxidc.com/topicnews.aspx?tid=2)使用crontab定时任务 [http://www.linuxidc.com//Linux/2013-05/84770.htm](http://www.linuxidc.com/Linux/2013-05/84770.htm)

Linux计划任务(at batch crontab anacron) [http://www.linuxidc.com//Linux/2013-03/81584.htm](http://www.linuxidc.com/Linux/2013-03/81584.htm)

Linux任务计划 (at,crontab) <http://www.linuxidc.com/Linux/2015-09/122970.htm>

**本文永久更新链接地址**：<http://www.linuxidc.com/Linux/2016-02/128323.htm>