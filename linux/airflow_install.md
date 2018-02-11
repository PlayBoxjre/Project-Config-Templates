# Airflow安装-Linux

#### 安装lxml时gcc: internal compiler error: Killed (program cc1)的解决方法

```shell

在安装lxml时出现如下错误

gcc: internal compiler error: Killed (program cc1)
通过查看dmesg发现下述错误信息
[2517343.500178] Out of memory: Kill process 5051 (cc1) score 632 or sacrifice child
[2517343.501833] Killed process 5051 (cc1) total-vm:471664kB, anon-rss:326648kB, file-rss:0kB
[2517441.995124] systemd-journald[233]: Vacuuming done, freed 4194304 byte

看来主要问题是因为内存不足导致的，为解决该问题通过增加swap分区来解决，具体方法如下：

sudo mkdir -p /var/cache/swap/
sudo dd if=/dev/zero of=/var/cache/swap/swap0 bs=1M count=512
sudo chmod 0600 /var/cache/swap/swap0
sudo mkswap /var/cache/swap/swap0 
sudo swapon /var/cache/swap/swap0


参考资料：
http://stackoverflow.com/questions/19761226/how-to-compile-ruby-with-rvm-on-a-low-memory-system
https://github.com/pydata/pandas/issues/1880#issuecomment-9920484
```

 

# [Python中lxml模块的安装](http://blog.csdn.net/jerry_1126/article/details/43758179)

标签： [python](http://www.csdn.net/tag/python)[linux](http://www.csdn.net/tag/linux)[windows](http://www.csdn.net/tag/windows)

2015-02-12 18:09 26704人阅读 [评论](http://blog.csdn.net/jerry_1126/article/details/43758179#comments)(3) [收藏](javascript:void(0);) [举报](http://blog.csdn.net/jerry_1126/article/details/43758179#report)

![img](http://static.blog.csdn.net/images/category_icon.jpg) 分类：

【开发工具】*（26）* ![img](http://static.blog.csdn.net/images/arrow_triangle%20_down.jpg) 【编程语言】*（292）* ![img](http://static.blog.csdn.net/images/arrow_triangle%20_down.jpg)

版权声明：本文为博主原创文章，未经博主允许不得转载。

lxml是Python中与XML及HTML相关功能中最丰富和最容易使用的库。lxml并不是Python自带的包，而是为libxml2和libxslt库的一个Python化的绑定。它与众不同的地方是它兼顾了这些库的速度和功能完整性，以及纯Python API的简洁性，与大家熟知的ElementTree API兼容但比之更优越！但安装lxml却又有点麻烦，因为存在依赖，直接安装的话用easy_install, pip都不能成功，会报gcc错误。下面列出来Windows、Linux下面的安装方法:

【**Windows系统**】

先确保Python已经安装好，环境变量也配置好了，相应的的easy_install、pip也安装好了.

**1. 执行 pip install virtualenv**

1. C:\>pip install virtualenv  
2. Requirement already satisfied (use --upgrade to upgrade): virtualenv in c:\python27\lib\site-package  
3. s\virtualenv-12.0.4-py2.7.egg  

**2. 从官方网站下载与系统，Python版本匹配的lxml文件**：

<http://pypi.python.org/pypi/lxml/2.3/> 

**NOTE:**

比如说我的电脑是Python 2.7.4, 64位操作系统，那么我就可以下载

1. lxml-2.3-py2.7-win-amd64.egg (md5)     # Python Egg  
2. 或  
3. lxml-2.3.win-amd64-py2.7.exe (md5)     # MS Windows installer  

**3.** **执行 easy_install lxml-2.3-py2.7-win-amd64.egg**

1. D:\Downloads>easy_install lxml-2.3-py2.7-win-amd64.egg    # 进入该文件所在目录执行该命令  
2. Processing lxml-2.3-py2.7-win-amd64.egg  
3. creating c:\python27\lib\site-packages\lxml-2.3-py2.7-win-amd64.egg  
4. Extracting lxml-2.3-py2.7-win-amd64.egg to c:\python27\lib\site-packages  
5. Adding lxml 2.3 to easy-install.pth file  
6. ​
7. ​
8. Installed c:\python27\lib\site-packages\lxml-2.3-py2.7-win-amd64.egg  
9. Processing dependencies for lxml==2.3  
10. Finished processing dependencies for lxml==2.3  

**\*NOTE:***

**1. **可用exe可执行文件，方法更简单直接安装就可以

**2.** 可用easy_install安装方式，也可以用pip的方式

1. \#再执行下，就安装成功了！  
2. \>>> import lxml     
3. \>>>   

**3.** 如用pip安装，常用命令就是:

- *pip install simplejson*                      # 安装Python包
- *pip install --upgrade simplejson*          # 升级Python包
- *pip uninstall simplejson*                    # 卸载Python包

**4.** 如用Eclipse+Pydev的开发方式，需要移除旧包，重新加载一次

- *Window --> Preferences --> PyDev --> Interperter-python*   # 否则导包的时候会报错

Linux系统

】

因为lxml依赖的包如下:

libxml2, libxml2-devel, libxlst, libxlst-devel, python-libxml2, python-libxslt

所以安装步骤如下:

**第一步: 安装 libxml2**

- $ *sudo apt-get install libxml2 libxml2-dev*  

**第二步: 安装 libxslt**

- $ *sudo apt-get install libxlst libxslt-dev* 

**第三步: 安装 python-libxml2 和 python-libxslt**

- $ *sudo apt-get install python-libxml2 python-libxslt*

**第四步: 安装 lxml**

- $ *sudo easy_install lxml*

参考官方文档:

<http://codespeak.net/lxml/installation.html>



- [![img](http://static.blog.csdn.net/images/ico_list.gif)目录视图](http://blog.csdn.net/sunnyyoona?viewmode=contents)
- [![img](http://static.blog.csdn.net/images/ico_summary.gif)摘要视图](http://blog.csdn.net/sunnyyoona?viewmode=list)
- [![img](http://static.blog.csdn.net/images/ico_rss.gif)订阅](http://blog.csdn.net/sunnyyoona/rss/list)

 

# [[AirFlow\]AirFlow使用指南三 第一个DAG示例](http://blog.csdn.net/sunnyyoona/article/details/76615699)

标签： [AirFlow](http://www.csdn.net/tag/AirFlow)[工作流调度](http://www.csdn.net/tag/%e5%b7%a5%e4%bd%9c%e6%b5%81%e8%b0%83%e5%ba%a6)[任务调度](http://www.csdn.net/tag/%e4%bb%bb%e5%8a%a1%e8%b0%83%e5%ba%a6)[DAG](http://www.csdn.net/tag/DAG)

2017-08-03 12:00 4063人阅读 [评论](http://blog.csdn.net/sunnyyoona/article/details/76615699#comments)(2) [收藏](javascript:void(0);) [举报](http://blog.csdn.net/sunnyyoona/article/details/76615699#report)

![img](http://static.blog.csdn.net/images/category_icon.jpg) 分类：

Big-Data*（21）* ![img](http://static.blog.csdn.net/images/arrow_triangle%20_down.jpg)

版权声明：本文为博主原创文章，未经博主允许不得转载。 <http://blog.csdn.net/sunnyyoona/article/details/76615699>

目录[(?)](http://blog.csdn.net/sunnyyoona/article/details/76615699#)[[+\]](http://blog.csdn.net/sunnyyoona/article/details/76615699#)

经过前两篇文章的简单介绍之后，我们安装了自己的AirFlow以及简单了解了DAG的定义文件．现在我们要实现自己的一个DAG．

## 1. 启动Web服务器

使用如下命令启用:

```
airflow webserver

```

现在可以通过将浏览器导航到启动Airflow的主机上的8080端口来访问Airflow UI，例如：http://localhost:8080/admin/

![img](http://img.blog.csdn.net/20170802173129848?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU3VubnlZb29uYQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

备注

```
Airflow附带了许多示例DAG。 请注意，在你自己的`dags_folder`中至少有一个DAG定义文件之前，这些示例可能无法正常工作。你可以通过更改`airflow.cfg`中的`load_examples`设置来隐藏示例DAG。

```

### 2. 第一个AirFlow DAG

现在一切都准备好了，我们开始写一些代码，来实现我们的第一个DAG。 我们将首先创建一个Hello World工作流程，其中除了向日志发送"Hello world！"之外什么都不做。

创建你的`dags_folder`，那就是你的DAG定义文件存储目录---`$AIRFLOW_HOME/dags`。在该目录中创建一个名为hello_world.py的文件。

```
AIRFLOW_HOME
├── airflow.cfg
├── airflow.db
├── airflow-webserver.pid
├── dags
│   ├── hello_world.py
│   └── hello_world.pyc
└── unittests.cfg

```

将以下代码添加到`dags/hello_world.py`中:

```
# -*- coding: utf-8 -*-

import airflow
from airflow import DAG
from airflow.operators.bash_operator import BashOperator
from airflow.operators.python_operator import PythonOperator
from datetime import timedelta

#-------------------------------------------------------------------------------
# these args will get passed on to each operator
# you can override them on a per-task basis during operator initialization

default_args = {
    'owner': 'jifeng.si',
    'depends_on_past': False,
    'start_date': airflow.utils.dates.days_ago(2),
    'email': ['1203745031@qq.com'],
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
    # 'queue': 'bash_queue',
    # 'pool': 'backfill',
    # 'priority_weight': 10,
    # 'end_date': datetime(2016, 1, 1),
    # 'wait_for_downstream': False,
    # 'dag': dag,
    # 'adhoc':False,
    # 'sla': timedelta(hours=2),
    # 'execution_timeout': timedelta(seconds=300),
    # 'on_failure_callback': some_function,
    # 'on_success_callback': some_other_function,
    # 'on_retry_callback': another_function,
    # 'trigger_rule': u'all_success'
}

#-------------------------------------------------------------------------------
# dag

dag = DAG(
    'example_hello_world_dag',
    default_args=default_args,
    description='my first DAG',
    schedule_interval=timedelta(days=1))

#-------------------------------------------------------------------------------
# first operator

date_operator = BashOperator(
    task_id='date_task',
    bash_command='date',
    dag=dag)

#-------------------------------------------------------------------------------
# second operator

sleep_operator = BashOperator(
    task_id='sleep_task',
    depends_on_past=False,
    bash_command='sleep 5',
    dag=dag)

#-------------------------------------------------------------------------------
# third operator

def print_hello():
    return 'Hello world!'

hello_operator = PythonOperator(
    task_id='hello_task',
    python_callable=print_hello,
    dag=dag)

#-------------------------------------------------------------------------------
# dependencies

sleep_operator.set_upstream(date_operator)
hello_operator.set_upstream(date_operator)

```

该文件创建一个简单的DAG，只有三个运算符，两个BaseOperator(一个打印日期一个休眠5秒)，另一个为PythonOperator在执行任务时调用print_hello函数。

### 3. 测试代码

使用如下命令测试一下我们写的代码的正确性:

```
python ~/opt/airflow/dags/hello_world.py

```

如果你的脚本没有抛出异常，这意味着你代码中没有错误，并且你的Airflow环境是健全的。

下面测试一下我们的DAG中的Task．使用如下命令查看我们`example_hello_world_dag`DAG下有什么Task:

```
xiaosi@yoona:~$ airflow list_tasks example_hello_world_dag
[2017-08-03 11:41:57,097] {__init__.py:57} INFO - Using executor SequentialExecutor
[2017-08-03 11:41:57,220] {driver.py:120} INFO - Generating grammar tables from /usr/lib/python2.7/lib2to3/Grammar.txt
[2017-08-03 11:41:57,241] {driver.py:120} INFO - Generating grammar tables from /usr/lib/python2.7/lib2to3/PatternGrammar.txt
[2017-08-03 11:41:57,490] {models.py:167} INFO - Filling up the DagBag from /home/xiaosi/opt/airflow/dags
date_task
hello_task
sleep_task

```

可以看到我们有三个Task：

```
date_task
hello_task
sleep_task

```

下面分别测试一下这几个Task:

(1) 测试date_task

```
xiaosi@yoona:~$ airflow test example_hello_world_dag date_task 20170803
...
--------------------------------------------------------------------------------
Starting attempt 1 of 2
--------------------------------------------------------------------------------

[2017-08-03 11:44:02,248] {models.py:1342} INFO - Executing <Task(BashOperator): date_task> on 2017-08-03 00:00:00
[2017-08-03 11:44:02,258] {bash_operator.py:71} INFO - tmp dir root location:
/tmp
[2017-08-03 11:44:02,259] {bash_operator.py:80} INFO - Temporary script location :/tmp/airflowtmpxh6da9//tmp/airflowtmpxh6da9/date_tasktQQB0V
[2017-08-03 11:44:02,259] {bash_operator.py:81} INFO - Running command: date
[2017-08-03 11:44:02,264] {bash_operator.py:90} INFO - Output:
[2017-08-03 11:44:02,265] {bash_operator.py:94} INFO - 2017年 08月 03日 星期四 11:44:02 CST
[2017-08-03 11:44:02,266] {bash_operator.py:97} INFO - Command exited with return code 0

```

(2) 测试hello_task

```
xiaosi@yoona:~$ airflow test example_hello_world_dag hello_task 20170803
...
--------------------------------------------------------------------------------
Starting attempt 1 of 2
--------------------------------------------------------------------------------

[2017-08-03 11:45:29,546] {models.py:1342} INFO - Executing <Task(PythonOperator): hello_task> on 2017-08-03 00:00:00
[2017-08-03 11:45:29,551] {python_operator.py:81} INFO - Done. Returned value was: Hello world!

```

(3) 测试sleep_task

```
xiaosi@yoona:~$ airflow test example_hello_world_dag sleep_task 20170803
...
--------------------------------------------------------------------------------
Starting attempt 1 of 2
--------------------------------------------------------------------------------

[2017-08-03 11:46:23,970] {models.py:1342} INFO - Executing <Task(BashOperator): sleep_task> on 2017-08-03 00:00:00
[2017-08-03 11:46:23,981] {bash_operator.py:71} INFO - tmp dir root location:
/tmp
[2017-08-03 11:46:23,983] {bash_operator.py:80} INFO - Temporary script location :/tmp/airflowtmpsuamQx//tmp/airflowtmpsuamQx/sleep_taskuKYlrh
[2017-08-03 11:46:23,983] {bash_operator.py:81} INFO - Running command: sleep 5
[2017-08-03 11:46:23,988] {bash_operator.py:90} INFO - Output:
[2017-08-03 11:46:28,990] {bash_operator.py:97} INFO - Command exited with return code 0

```

如果没有问题，我们就可以运行我们的DAG了．

### 4. 运行DAG

为了运行你的DAG，打开另一个终端，并通过如下命令来启动Airflow调度程序:

```
airflow scheduler

```

备注

```
调度程序将发送任务进行执行。默认Airflow设置依赖于一个名为`SequentialExecutor`的执行器，它由调度程序自动启动。在生产中，你可以使用更强大的执行器，如`CeleryExecutor`。

```

当你在浏览器中重新加载Airflow UI时，应该会在Airflow UI中看到你的`hello_world` DAG。

![img](http://img.blog.csdn.net/20170803094344833?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU3VubnlZb29uYQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

为了启动DAG Run，首先打开工作流(off键)，然后单击`Trigger Dag`按钮(Links 第一个按钮)，最后单击`Graph View`按钮(Links 第三个按钮)以查看运行进度:

![img](http://img.blog.csdn.net/20170803111755665?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU3VubnlZb29uYQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

你可以重新加载图形视图，直到两个任务达到状态成功。完成后，你可以单击hello_task，然后单击`View Log`查看日志。如果一切都按预期工作，日志应该显示一些行，其中之一是这样的：

```
...
[2017-08-03 09:46:43,213] {base_task_runner.py:95} INFO - Subtask: --------------------------------------------------------------------------------
[2017-08-03 09:46:43,213] {base_task_runner.py:95} INFO - Subtask: Starting attempt 1 of 2
[2017-08-03 09:46:43,214] {base_task_runner.py:95} INFO - Subtask: --------------------------------------------------------------------------------
[2017-08-03 09:46:43,214] {base_task_runner.py:95} INFO - Subtask:
[2017-08-03 09:46:43,228] {base_task_runner.py:95} INFO - Subtask: [2017-08-03 09:46:43,228] {models.py:1342} INFO - Executing <Task(PythonOperator): hello_task> on 2017-08-03 09:45:49.070859
[2017-08-03 09:46:43,236] {base_task_runner.py:95} INFO - Subtask: [2017-08-03 09:46:43,235] {python_operator.py:81} INFO - Done. Returned value was: Hello world!
[2017-08-03 09:46:47,378] {jobs.py:2083} INFO - Task exited with return code 0
```

[ ](javascript:void(0);)

# Airflow 安装总结(4)-DAG实例(执行MySql存储过程)

## DAG样例（执行MySql存储过程）

Airflow通过MySqlOperator执行sql语句，项目中需要执行带参数的存储过程，具体的DAG样例如下：

```
from airflow import DAG
from airflow.operators import BashOperator, DummyOperator,MySqlOperator
from airflow.models import DAG
from datetime import datetime, timedelta

# 定义一些时间参数
seven_days_ago = datetime.combine(datetime.today() - timedelta(7), datetime.min.time())
one_day_ago    = datetime.combine(datetime.today() - timedelta(1), datetime.min.time())
deal_date      = datetime.strftime(datetime.combine(datetime.today() - timedelta(1), datetime.min.time()), '%Y%m%d')

default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2016, 3, 15),
    'email': ['airflow@airflow.com'],
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
    # 'queue': 'bash_queue',
    # 'pool': 'backfill',
    # 'priority_weight': 10,
    # 'schedule_interval': timedelta(1),
    # 'end_date': datetime(2016, 1, 1),
}

dag = DAG('dwd_rpt_m', default_args=default_args, schedule_interval='0 3 * * *')

# MySqlOperator
sql1 = ["set @v_txdate = %s" %deal_date,
       "set @v_retcode = 0",
       "call dw_dev.p_test_1(@v_txdate, @v_retcode)"
      ]

# guoshu_dev 需要在WebUi中设置变量
t1 = MySqlOperator(
    mysql_conn_id='dev',
    sql = sql1,
    task_id='p_test_1',
    dag=dag)

sql2 = ["set @v_txdate = %s" %deal_date,
       "set @v_retcode = 0",
       "call dw_dev.p_test_2(@v_txdate, @v_retcode)"
      ]

t2 = MySqlOperator(
    mysql_conn_id='dev',
    sql = sql2,
    task_id='p_test_2',
    dag=dag)

t2.set_upstream(t1)

```

小礼物走一走，来简书关注我



# Airflow使用入门指南

原创 2016年11月07日 13:50:52

- 标签：
- [python](http://so.csdn.net/so/search/s.do?q=python&t=blog) /
- [workflow](http://so.csdn.net/so/search/s.do?q=workflow&t=blog) /
- [airflow](http://so.csdn.net/so/search/s.do?q=airflow&t=blog)


- **20137

### Airflow能做什么

关注公众号， 查看更多 <http://mp.weixin.qq.com/s/xPjXMc_6ssHt16J07BC7jA>

[Airflow](https://airflow.incubator.apache.org/index.html)是一个工作流分配管理系统，通过有向非循环图的方式管理任务流程，设置任务依赖关系和时间调度。

Airflow独立于我们要运行的任务，只需要把任务的名字和运行方式提供给Airflow作为一个task就可以。

### 安装和使用

#### 最简单安装

在Linux终端运行如下命令 (需要已安装好`python2.x`和`pip`)：

```
pip install airflow
pip install "airflow[crypto, password]"12
```

安装成功之后，执行下面三步，就可以使用了。默认是使用的`SequentialExecutor`, 只能顺次执行任务。

- 初始化数据库 `airflow initdb` [必须的步骤]
- 启动web服务器 `airflow webserver -p 8080` [方便可视化管理dag]
- 启动任务 `airflow scheduler` [scheduler启动后，DAG目录下的dags就会根据设定的时间定时启动]
- 此外我们还可以直接测试单个DAG，如测试文章末尾的DAG `airflow test ct1 print_date 2016-05-14`

最新版本的Airflow可从<https://github.com/apache/incubator-airflow>下载获得，解压缩按照安装python包的方式安装。

#### 配置 `mysql`以启用`LocalExecutor`和`CeleryExecutor`

- 安装mysql数据库支持

  ```
  yum install mysql mysql-server
  pip install airflow[mysql]12
  ```

- 设置mysql根用户的密码

  ```
  ct@server:~/airflow: mysql -uroot #以root身份登录mysql，默认无密码
  mysql> SET PASSWORD=PASSWORD("passwd");
  mysql> FLUSH PRIVILEGES; 

  # 注意sql语句末尾的分号
  123456
  ```

- 新建用户和数据库

  ```
  # 新建名字为<airflow>的数据库

  mysql> CREATE DATABASE airflow; 

  # 新建用户`ct`，密码为`152108`, 该用户对数据库`airflow`有完全操作权限


  mysql> GRANT all privileges on airflow.* TO 'ct'@'localhost'  IDENTIFIED BY '152108'; 
  mysql> FLUSH PRIVILEGES; 12345678910
  ```

- 修改airflow配置文件支持mysql

  - `airflow.cfg` 文件通常在`~/airflow`目录下

  - 更改数据库链接

    ```
    sql_alchemy_conn = mysql://ct:152108@localhost/airflow
    对应字段解释如下： dialect+driver://username:password@host:port/database12
    ```

  - 初始化数据库 `airflow initdb`

  - 初始化数据库成功后，可进入mysql查看新生成的数据表。

    ```
    ct@server:~/airflow: mysql -uct -p152108
    mysql> USE airflow;
    mysql> SHOW TABLES;
    +-------------------+
    | Tables_in_airflow |
    +-------------------+
    | alembic_version   |
    | chart             |
    | connection        |
    | dag               |
    | dag_pickle        |
    | dag_run           |
    | import_error      |
    | job               |
    | known_event       |
    | known_event_type  |
    | log               |
    | sla_miss          |
    | slot_pool         |
    | task_instance     |
    | users             |
    | variable          |
    | xcom              |
    +-------------------+
    17 rows in set (0.00 sec)12345678910111213141516171819202122232425
    ```

- centos7中使用mariadb取代了mysql, 但所有命令的执行相同

  ```
  yum install mariadb mariadb-server
  systemctl start mariadb ==> 启动mariadb
  systemctl enable mariadb ==> 开机自启动
  mysql_secure_installation ==> 设置 root密码等相关
  mysql -uroot -p123456 ==> 测试登录！12345
  ```

#### 配置LocalExecutor

注：作为测试使用，此步可以跳过, 最后的生产环境用的是CeleryExecutor; 若CeleryExecutor配置不方便，也可使用LocalExecutor。

前面数据库已经配置好了，所以如果想使用LocalExecutor就只需要修改airflow配置文件就可以了。`airflow.cfg` 文件通常在`~/airflow`目录下，打开更改`executor`为 `executor = LocalExecutor`即完成了配置。

把文后[TASK](http://blog.csdn.net/qazplm12_3/article/details/53065654#task)部分的dag文件拷贝几个到`~/airflow/dags`目录下，顺次执行下面的命令，然后打开网址[http://127.0.0.1:8080](http://127.0.0.1:8080/)就可以实时侦测任务动态了：

```
ct@server:~/airflow: airflow initdb` (若前面执行过，就跳过)
ct@server:~/airflow: airflow webserver --debug &
ct@server:~/airflow: airflow scheduler123
```

#### 配置CeleryExecutor (rabbitmq支持)

- 安装airflow的celery和rabbitmq组件

  ```
  pip install airflow[celery]
  pip install airflow[rabbitmq]12
  ```

- 安装erlang和rabbitmq

  - 如果能直接使用`yum`或`apt-get`安装则万事大吉。
  - 我使用的CentOS6则不能，需要如下一番折腾，

  ```
  # (Centos6,[REF](http://www.rabbitmq.com/install-rpm.html))

  wget https://packages.erlang-solutions.com/erlang/esl-erlang/FLAVOUR_1_general/esl-erlang_18.3-1~centos~6_amd64.rpm
  yum install esl-erlang_18.3-1~centos~6_amd64.rpm
  wget https://github.com/jasonmcintosh/esl-erlang-compat/releases/download/1.1.1/esl-erlang-compat-18.1-1.noarch.rpm
  yum install esl-erlang-compat-18.1-1.noarch.rpm
  wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.1/rabbitmq-server-3.6.1-1.noarch.rpm
  yum install rabbitmq-server-3.6.1-1.noarch.rpm123456789
  ```

- 配置rabbitmq

  - 启动rabbitmq: `rabbitmq-server -detached`

  - 开机启动rabbitmq: `chkconfig rabbitmq-server on`

  - 配置rabbitmq ([REF](http://docs.celeryproject.org/en/latest/getting-started/brokers/rabbitmq.html))

    ```
    rabbitmqctl add_user ct 152108
    rabbitmqctl add_vhost ct_airflow
    rabbitmqctl set_user_tags ct airflow
    rabbitmqctl set_permissions -p ct_airflow ct ".*" ".*" ".*"
    rabbitmq-plugins enable rabbitmq_management # no usage12345
    ```

- 修改airflow配置文件支持Celery

  - `airflow.cfg` 文件通常在`~/airflow`目录下

  - 更改executor为 `executor = CeleryExecutor`

  - 更改[broker_url](http://docs.celeryproject.org/en/latest/getting-started/brokers/rabbitmq.html)

    ```
    broker_url = amqp://ct:152108@localhost:5672/ct_airflow
    Format explanation: transport://userid:password@hostname:port/virtual_host12
    ```

  - 更改[celery_result_backend](http://docs.celeryproject.org/en/latest/configuration.html#conf-database-result-backend),

    ```
    # 可以与broker_url相同

    celery_result_backend = amqp://ct:152108@localhost:5672/ct_airflow
    Format explanation: transport://userid:password@hostname:port/virtual_host12345
    ```

- 测试

  - 启动服务器：`airflow webserver --debug`

  - 启动celery worker (不能用根用户)：`airflow worker`

  - 启动scheduler: `airflow scheduler`

  - 提示：

     

    ​

    - 测试过程中注意观察运行上面3个命令的3个窗口输出的日志
    - 当遇到不符合常理的情况时考虑清空 `airflow backend`的数据库, 可使用`airflow resetdb`清空。
    - 删除dag文件后，webserver中可能还会存在相应信息，这时需要重启webserver并刷新网页。
    - 关闭webserver: `ps -ef|grep -Ei '(airflow-webserver)'| grep master | awk '{print $2}'|xargs -i kill {}`

#### 一个脚本控制airflow系统的启动和重启

```
#!/bin/bash

#set -x
#set -e
set -u

usage()
{
cat <<EOF
${txtcyn}
Usage:

$0 options${txtrst}

${bldblu}Function${txtrst}:

This script is used to start or restart webserver service.

${txtbld}OPTIONS${txtrst}:
    -S  Start airflow system [${bldred}Default FALSE${txtrst}]
    -s  Restart airflow server only [${bldred}Default FALSE${txtrst}]
    -a  Restart all airflow programs including webserver, worker and
        scheduler. [${bldred}Default FALSE${txtrst}]
EOF
}

start_all=
server_only=
all=

while getopts "hs:S:a:" OPTION
do
    case $OPTION in
        h)
            usage
            exit 1
            ;;
        S)
            start_all=$OPTARG
            ;;
        s)
            server_only=$OPTARG
            ;;
        a)
            all=$OPTARG
            ;;
        ?)
            usage
            exit 1
            ;;
    esac
done

if [ -z "$server_only" ] && [ -z "$all" ] && [ -z "${start_all}" ]; then
    usage
    exit 1
fi

if [ "$server_only" == "TRUE" ]; then
    ps -ef | grep -Ei '(airflow-webserver)' | grep master | \
        awk '{print $2}' | xargs -i kill {}
    cd ~/airflow/
    nohup airflow webserver >webserver.log 2>&1 &
fi

if [ "$all" == "TRUE" ]; then
    ps -ef | grep -Ei 'airflow' | grep -v 'grep' | awk '{print $2}' | xargs -i kill {}
    cd ~/airflow/
    nohup airflow webserver >>webserver.log 2>&1 &
    nohup airflow worker >>worker.log 2>&1 &
    nohup airflow scheduler >>scheduler.log 2>&1 &
fi


if [ "${start_all}" == "TRUE" ]; then
    cd ~/airflow/
    nohup airflow webserver >>webserver.log 2>&1 &
    nohup airflow worker >>worker.log 2>&1 &
    nohup airflow scheduler >>scheduler.log 2>&1 &
fi
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081
```

### airflow.cfg 其它配置

- dags_folder

  `dags_folder`目录支持子目录和软连接，因此不同的dag可以分门别类的存储起来。

- 设置邮件发送服务

  ```
  smtp_host = smtp.163.com
  smtp_starttls = True
  smtp_ssl = False
  smtp_user = username@163.com
  smtp_port = 25
  smtp_password = userpasswd
  smtp_mail_from = username@163.com1234567
  ```

- 多用户登录设置 (似乎只有CeleryExecutor支持)

  - 修改`airflow.cfg`中的下面3行配置

  ```
  authenticate = True
  auth_backend = airflow.contrib.auth.backends.password_auth
  filter_by_owner = True123
  ```

  - 增加一个用户(在airflow所在服务器的python下运行)

  ```
  import airflow
  from airflow import models,   settings
  from airflow.contrib.auth.backends.password_auth import PasswordUser
  user = PasswordUser(models.User())
  user.username = 'ehbio'
  user.email = 'mail@ehbio.com'
  user.password = 'ehbio'
  session = settings.Session()
  session.add(user)
  session.commit()
  session.close()
  exit()123456789101112
  ```

### TASK

- 参数解释

  - `depends_on_past`

    Airflow assumes idempotent tasks that operate on immutable data 
    chunks. It also assumes that all task instance (each task for each 
    schedule) needs to run.

    If your tasks need to be executed sequentially, you need to 
    tell Airflow: use the `depends_on_past=True` flag on the tasks 
    that require sequential execution.)

    如果在TASK本该运行却没有运行时，或者设置的`interval`为`@once`时，推荐使用`depends_on_past=False`。我在运行dag时，有时会出现，明明上游任务已经运行结束，下游任务却没有启动，整个dag就卡住了。这时设置`depends_on_past=False`可以解决这类问题。

  - `timestamp` in format like `2016-01-01T00:03:00`

  - Task中调用的命令出错后需要在网站`Graph view`中点击`run`手动重启。 
    为了方便任务修改后的顺利运行，有个折衷的方法是：

    - 设置 `email_on_retry: True`
    - 设置较长的`retry_delay`，方便在收到邮件后，能有时间做出处理
    - 然后再修改为较短的`retry_delay`，方便快速启动

- 写完task DAG后，一定记得先检测下有无语法错误 `python dag.py`

- 测试文件1：ct1.py

  ```
  from airflow import DAG
  from airflow.operators import BashOperator, MySqlOperator

  from datetime import datetime, timedelta

  one_min_ago = datetime.combine(datetime.today() -
  timedelta(minutes=1), datetime.min.time())

  default_args = {
    'owner': 'airflow',         

    #为了测试方便，起始时间一般为当前时间减去schedule_interval
    'start_date': datatime(2016, 5, 29, 8, 30), 
    'email': ['chentong_biology@163.com'],
    'email_on_failure': False, 
    'email_on_retry': False, 
    'depends_on_past': False, 
    'retries': 1, 
    'retry_delay': timedelta(minutes=5), 
    #'queue': 'bash_queue',
    #'pool': 'backfill', 
    #'priority_weight': 10, 
    #'end_date': datetime(2016, 5, 29, 11, 30), 
  }


  # DAG id 'ct1'必须在airflow中是unique的, 一般与文件名相同


  # 多个用户时可加用户名做标记

  dag = DAG('ct1', default_args=default_args,
    schedule_interval="@once")

  t1 = BashOperator(
    task_id='print_date', 
    bash_command='date', 
    dag=dag)


  #cmd = "/home/test/test.bash " 注意末尾的空格

  t2 = BashOperator(
    task_id='echo', 
    bash_command='echo "test" ', 
    retries=3, 
    dag=dag)

  templated_command = """
    {% for i in range(2) %}
        echo "{{ ds }}" 
        echo "{{ macros.ds_add(ds, 7) }}"
        echo "{{ params.my_param }}"
    {% endfor %}
  """
  t3 = BashOperator(
    task_id='templated', 
    bash_command=templated_command, 
    params={'my_param': "Parameter I passed in"}, 
    dag=dag)


  # This means that t2 will depend on t1 running successfully to run


  # It is equivalent to t1.set_downstream(t2)

  t2.set_upstream(t1)

  t3.set_upstream(t1)


  # all of this is equivalent to


  # dag.set_dependency('print_date', 'sleep')


  # dag.set_dependency('print_date', 'templated')
  1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162636465666768697071727374757677787980
  ```

- 测试文件2: `ct2.py`

  ```
  from airflow import DAG
  from airflow.operators import BashOperator

  from datetime import datetime, timedelta

  one_min_ago = datetime.combine(datetime.today() - timedelta(minutes=1),
                                  datetime.min.time())

  default_args = {
    'owner': 'airflow',         
    'depends_on_past': True, 
    'start_date': one_min_ago,
    'email': ['chentong_biology@163.com'],
    'email_on_failure': True, 
    'email_on_retry': True, 
    'retries': 5, 
    'retry_delay': timedelta(hours=30), 
    #'queue': 'bash_queue',
    #'pool': 'backfill', 
    #'priority_weight': 10, 
    #'end_date': datetime(2016, 5, 29, 11, 30), 
  }

  dag = DAG('ct2', default_args=default_args,
    schedule_interval="@once")

  t1 = BashOperator(
    task_id='run1', 
    bash_command='(cd /home/ct/test; bash run1.sh -f ct_t1) ', 
    dag=dag)

  t2 = BashOperator(
    task_id='run2', 
    bash_command='(cd /home/ct/test; bash run2.sh -f ct_t1) ', 
    dag=dag)

  t2.set_upstream(t1)
  1234567891011121314151617181920212223242526272829303132333435363738
  ```

- run1.sh

  ```
  #!/bin/bash



  #set -x

  set -e
  set -u

  usage()
  {
  cat <<EOF
  ${txtcyn}
  Usage:

  $0 options${txtrst}

  ${bldblu}Function${txtrst}:

  This script is used to do ********************.

  ${txtbld}OPTIONS${txtrst}:
  -f  Data file ${bldred}[NECESSARY]${txtrst}
  -z  Is there a header[${bldred}Default TRUE${txtrst}]
  EOF
  }

  file=
  header='TRUE'

  while getopts "hf:z:" OPTION
  do
  case $OPTION in
      h)
          usage
          exit 1
          ;;
      f)
          file=$OPTARG
          ;;
      z)
          header=$OPTARG
          ;;
      ?)
          usage
          exit 1
          ;;
  esac
  done

  if [ -z $file ]; then
  usage
  exit 1
  fi

  cat <<END >$file
  A
  B
  C
  D
  E
  F
  G
  END

  sleep 20s12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667
  ```

- run2.sh

  ```
  #!/bin/bash



  #set -x

  set -e
  set -u

  usage()
  {
  cat <<EOF
  ${txtcyn}
  Usage:

  $0 options${txtrst}

  ${bldblu}Function${txtrst}:

  This script is used to do ********************.

  ${txtbld}OPTIONS${txtrst}:
  -f  Data file ${bldred}[NECESSARY]${txtrst}
  EOF
  }

  file=
  header='TRUE'

  while getopts "hf:z:" OPTION
  do
  case $OPTION in
      h)
          usage
          exit 1
          ;;
      f)
          file=$OPTARG
          ;;
      ?)
          usage
          exit 1
          ;;
  esac
  done

  if [ -z $file ]; then
  usage
  exit 1
  fi

  awk 'BEGIN{OFS=FS="\t"}{print $0, "53"}' $file >${file}.out
  123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354
  ```

### 其它问题

- The DagRun object has room for a `conf` parameter that gets exposed 
  in the “context” (templates, operators, …). That is the place 
  where you would associate parameters to a specific run. For now this 
  is only possible in the context of an externally triggered DAG run. 
  The way the `TriggerDagRunOperator` works, you can fill in the conf 
  param during the execution of the callable that you pass to the 
  operator.

  If you are looking to change the shape of your DAG through parameters, 
  we recommend doing that using “singleton” DAGs (using a “@once” 
  `schedule_interval`), meaning that you would write a 
  Python program that generates multiple dag_ids, one of each run, 
  probably based on metadata stored in a config file or elsewhere.

  The idea is that if you use parameters to alter the shape of your 
  DAG, you break some of the assumptions around continuity of the 
  schedule. Things like visualizing the tree view or how to perform a 
  backfill becomes unclear and mushy. So if the shape of your DAG 
  changes radically based on parameters, we consider those to be 
  different DAGs, and you generate each one in your pipeline file.

- 完全删掉某个DAG的信息

  ```
  set @dag_id = 'BAD_DAG';
  delete from airflow.xcom where dag_id = @dag_id;
  delete from airflow.task_instance where dag_id = @dag_id;
  delete from airflow.sla_miss where dag_id = @dag_id;
  delete from airflow.log where dag_id = @dag_id;
  delete from airflow.job where dag_id = @dag_id;
  delete from airflow.dag_run where dag_id = @dag_id;
  delete from airflow.dag where dag_id = @dag_id;12345678
  ```

- supervisord自动管理进程

  ```
  [program:airflow_webserver]
  command=/usr/local/bin/python2.7 /usr/local/bin/airflow webserver
  user=airflow
  environment=AIRFLOW_HOME="/home/airflow/airflow", PATH="/usr/local/bin:%(ENV_PATH)s"
  stderr_logfile=/var/log/airflow-webserver.err.log
  stdout_logfile=/var/log/airflow-webserver.out.log

  [program:airflow_worker]
  command=/usr/local/bin/python2.7 /usr/local/bin/airflow worker
  user=airflow
  environment=AIRFLOW_HOME="/home/airflow/airflow", PATH="/usr/local/bin:%(ENV_PATH)s"
  stderr_logfile=/var/log/airflow-worker.err.log
  stdout_logfile=/var/log/airflow-worker.out.log

  [program:airflow_scheduler]
  command=/usr/local/bin/python2.7 /usr/local/bin/airflow scheduler
  user=airflow
  environment=AIRFLOW_HOME="/home/airflow/airflow", PATH="/usr/local/bin:%(ENV_PATH)s"
  stderr_logfile=/var/log/airflow-scheduler.err.log
  stdout_logfile=/var/log/airflow-scheduler.out.log1234567891011121314151617181920
  ```

- 在特定情况下，修改DAG后，为了避免当前日期之前任务的运行，可以使用`backfill`填补特定时间段的任务

  - `airflow backfill -s START -e END --mark_success DAG_ID`

### [端口转发](https://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/)

- 之前的配置都是在内网服务器进行的，但内网服务器只开放了SSH端口22,因此 
  我尝试在另外一台电脑上使用相同的配置，然后设置端口转发，把外网服务器 
  的rabbitmq的5672端口映射到内网服务器的对应端口，然后启动airflow连接 
  。

  - `ssh -v -4 -NF -R 5672:127.0.0.1:5672 aliyun`

  - 上一条命令表示的格式为

    `ssh -R <local port>:<remote host>:<remote port> <SSH hostname>`

    `local port`表示hostname的port

    `Remote connections from LOCALHOST:5672 forwarded to local address 127.0.0.1:5672`

  - -v: 在测试时打开

  - -4: 出现错误”bind: Cannot assign requested address”时，force the 
    ssh client to use ipv4

  - 若出现”Warning: remote port forwarding failed for listen port 52698” 
    ，关掉其它的ssh tunnel。

### 不同机器使用airflow

- 在外网服务器（用做任务分发服务器）配置与内网服务器相同的airflow模块
- 使用前述的端口转发以便外网服务器绕过内网服务器的防火墙访问`rabbitmq 5672`端口。
- 在外网服务器启动 airflow `webserver` `scheduler`, 在内网服务器启动 
  `airflow worker` 发现任务执行状态丢失。继续学习Celery，以解决此问题。

### 安装redis (最后没用到)

- <http://download.redis.io/releases/redis-3.2.0.tar.gz>
- `tar xvzf redis-3.2.0.tar.gz` and `make`
- `redis-server`启动redis
- 使用`ps -ef | grep 'redis'`检测后台进程是否存在
- 检测6379端口是否在监听`netstat -lntp | grep 6379`

### 任务未按预期运行可能的原因

- 检查 `start_date` 和`end_date`是否在合适的时间范围内
- 检查 `airflow worker`, `airflow scheduler`和 
  `airflow webserver --debug`的输出，有没有某个任务运行异常
- 检查airflow配置路径中`logs`文件夹下的日志输出
- 若以上都没有问题，则考虑数据冲突，解决方式包括清空数据库或着给当前 
  `dag`一个新的`dag_id`

### References

1. <https://pythonhosted.org/airflow/>
2. <http://kintoki.farbox.com/post/ji-chu-zhi-shi/airflow>
3. <http://www.jianshu.com/p/59d69981658a>
4. <http://bytepawn.com/luigi-airflow-pinball.html>
5. <https://github.com/airbnb/airflow>
6. <https://media.readthedocs.org/pdf/airflow/latest/airflow.pdf>
7. <http://www.csdn.net/article/1970-01-01/2825690>
8. <http://www.cnblogs.com/harrychinese/p/airflow.html>
9. <https://segmentfault.com/a/1190000005078547>

### 声明

文章原写于<http://blog.genesino.com/2016/05/airflow/>。转载请注明出处。



# Airflow实战

[**Yabuhoo!](http://ju.outofmemory.cn/feed/2919/) **2016-03-07 **4569** 阅读

 

在数据挖掘中， [ETL ](https://zh.wikipedia.org/wiki/ETL)是十分重要的一环。

在数据处理时，我们可能会用 `crontab `工具把任务例行起来。随着业务增多会发现任务的管理变得越来越吃力了，而[Airflow ](http://pythonhosted.org/airflow/)可能就是你想要找的那个方案。

![img](http://yabuhoo.qiniudn.com/images/2016/03/06.png)

#### 为什么要工作流管理平台

换句话说，传统的crontab任务管理存在什么不足：

1. 查看任务执行情况不直观方便；
2. 一些存在依赖关系的任务没办法保证；
3. 任务多了难以理清任务之间的关系。

#### 什么是Airflow

[Airflow ](http://pythonhosted.org/airflow/)是Airbnb内部发起的一个工作流(数据管道Data Pipeline)管理平台。

- 查看任务执行情况直观方便；

![img](http://yabuhoo.qiniudn.com/images/2016/03/07.png)

- 可以解决依赖性任务的高度；
- 任务之间的逻辑一目了然；

![img](http://yabuhoo.qiniudn.com/images/2016/03/08.png)

- 可追踪历史的任务执行情况；

![img](http://yabuhoo.qiniudn.com/images/2016/03/09.png)![img](http://yabuhoo.qiniudn.com/images/2016/03/10.png)

- Email通知；
- 可拓展性强(HDFS、Hive…)
- …

#### 下载安装与配置

\>> 安装

```
# 可选，默认是~/airflow
export AIRFLOW_HOME=~/.airflow

# 从pypi里安装airflow
pip install airflow

# 初始化数据库(即${AIRFLOW_HOME}/airflow.db)
airflow initdb
```

安装后 `airflow webserver `就可以开启后台管理界面了。

\>> 简单的登录密码设置

Airflow提供一个类似插件的方式让使用者可以更好地按自己的需求定制， [这里 ](http://pythonhosted.org/airflow/installation.html)列出了官方的一定Extra Packages.

默认安装后是，后台管理的webserver是无须登录，如果想加一个登录过程很简单：

首先安装密码验证模块。

```
pip install airflow[password]
```

有可能在安装的过程中会有一些依赖包没有，只需要相应地装上就可以了。比如 `libffi-dev `、 `flask-bcrypt`

然后在配置文件 `airflow.cfg `中的 `webserver `开启密码验证功能。

```
authenticate = true
auth_backend = airflow.contrib.auth.backends.password_auth
```

最后运行以下代码，将用户帐号密码信息写入DB。

```
import airflow
from airflow import models, settings
from airflow.contrib.auth.backends.password_auth import PasswordUser
user = PasswordUser(models.User())
user.username = 'user1'
user.email = 'user1@example.com'
user.password = 'passwd1'
session = settings.Session()
session.add(user)
session.commit()
session.close()
exit()
```

重启webserver即可看见登录页面。

\>> 设置Email功能

Email功能也是一个十分实用的功能，下面以126的smtp服务为例，在 `airflow.cfg `设置smtp：

```
smtp_host = smtp.126.com
smtp_starttls = True
smtp_user = example@126.com
smtp_port = 25
smtp_password = example
smtp_mail_from = example@126.com 
```

#### Airflow几个重要概念

- DAG(directed acyclic graphs):Airflow中用有向无环图表示务的依赖结构
- Task:单个任务节点
- Operator:任务图中一个任务节点的具体类型. 用的有BashOperator, DummyOperator

![img](http://yabuhoo.qiniudn.com/images/2016/03/11.png)

#### 怎么使用Airflow管理任务

首先，当然得打开后台守护进行，这样Airflow才能实时监控任务的调度情况

```shell
airflow scheduler
```

使用Airflow的大致步骤:

1. 写任务脚本(.py)
2. 测试任务脚本(command)
3. WebUI 自查

Step 1. 编写任务脚本(存放${AIRFLOW_HOME}/dags下)

下面是一个简单的示例：

```python
from airflow.operators import BashOperator, DummyOperator
from airflow.models import DAG
from datetime import datetime, timedelta

seven_days_ago = datetime.combine(datetime.today() - timedelta(7),
                                  datetime.min.time())
args = {
    'owner': 'xiaohei',
    'start_date': seven_days_ago,
    'email': ['xiaohei@126.com'],
    'email_on_failure': False
}

dag = DAG(
    dag_id='dag2', default_args=args,
    schedule_interval=‘@daily’  # 这里可以填crontab时间格式
    )

task0 = DummyOperator(task_id='task0', dag=dag)

cmd = 'ls -l'
task1 = BashOperator(
    task_id = 'task1',
    bash_command = cmd,
    dag = dag)

task0.set_downstream(task1)

task2 = DummyOperator(
        trigger_rule = 'all_done',
        task_id =  'task2',
        dag = dag,
        depends_on_past = True)

task2.set_upstream(task1)

task3  = DummyOperator(
        trigger_rule = 'all_done',
        depends_on_past = True,
        task_id = 'task3',
        dag = dag)

task3.set_upstream(task2)

task4 = BashOperator(
    task_id = 'task4',
    bash_command = 'lsfds-ljss',
    dag = dag)

task5 = DummyOperator(
        trigger_rule = 'all_done',
        task_id = 'task5',
        dag = dag)

task5.set_upstream(task4)
task5.set_upstream(task3)
```

这里有的 `start_date `有点特别，如果你设置了这个参数，那么airflow就会从start_date开始以 `schedule_interval `的规则开始执行，例如设置成3天前每小时执行一次，那么在调度正常启动时，就会立即调度 `24*3 `次，但注意，脚本执行环境的时间还是当前的系统时间，而不会说真是把系统时间模拟成3天前，所以感觉这个功能应用场景比较好限。

上面这个例子中，task1与task2是“弱依赖”关系——有时间上的关系，但父节点不一定都得成功执行。注意在task2中加上以下两句：

```
trigger_rule = 'all_done',
depends_on_past = True
```

![img](http://yabuhoo.qiniudn.com/images/2016/03/12.png)

task5需要直接父节点(task3 & task4)必须成功执行，但不一定全部父节点都得成功执行

```
trigger_rule = 'all_done',
```

![img](http://yabuhoo.qiniudn.com/images/2016/03/13.png)

Step 2. 测试任务脚本(command)

Example - ${AIRFLOW_HOME}/test_impoort.py：

```
from airflow.operators import BashOperator, DummyOperator
from airflow.models import DAG
from datetime import datetime, timedelta

seven_days_ago = datetime.combine(datetime.today() - timedelta(7),
                                  datetime.min.time())
args = {
    'owner': 'xiaohei',
    'start_date': seven_days_ago,
}

dag = DAG(
    dag_id='test_import_dag', default_args=args,
    schedule_interval='0 0 * * *',
    dagrun_timeout=timedelta(minutes=60))

run_test = BashOperator(
    task_id = 'test_import_task',
    bash_command = 'cd /home/jiwoDev/xiaohei/tmp; python test_import.py',
    dag = dag)
```

1. `$ cd ${AIRFLOW_HOME}/dags`
2. `$ python test_import.py `# 保证代码无语法错误
3. `$ airflow list_dags `# 查看dag是否成功加载
4. `airflow list_tasks test_import_dag –tree `# 查看dag的树形结构是否正确
5. `$ airflow test test_import_dag \ test_import_task 2016-3-7 `# 测试具体的dag的某个task在某个时间的运行是否正常
6. `$ airflow backfill test_import_dag -s 2016-3-4 \ -e 2016-3-7 `# 对dag进行某段时间内的完整测试

##### Step 3. WebUI自查

![img](http://yabuhoo.qiniudn.com/images/2016/03/14.png)![img](http://yabuhoo.qiniudn.com/images/2016/03/15.png)

#### 相关资源

源码： [https://github.com/airbnb/airflow](https://github.com/airbnb/airflow/)

官方文档： <http://pythonhosted.org/airflow/>

[点赞](javascript:void(0))

 