# MySQL57 安装 for Linux（Ubuntu16.04)

- 下载MySql

  ```bash
  1. sudo apt-get install mysql-server

  2. apt-get isntall mysql-client

  3.  sudo apt-get install libmysqlclient-dev
  ```

  >  安装过程中会提示设置密码什么的，注意设置了不要忘了，安装完成之后可以使用如下命令来检查是否安装成功：
  >
  > ```bash
  >  sudo netstat -tap | grep mysql
  > ```

- 配置MySql

  - 配置`root`密码

    > apt-get 之后会提示设置密码

  - 配置外网访问

    ```mysql
    update user set host = ’%’ where user = ’root’;
    ```

  - 配置用户权限

    ```mysql
    grant all privileges  on *.* to root@'%' identified by "root";
    ```

    ​

  ​