# ubuntu16.04中将python3设置为默认

转载 2017年04月11日 07:32:59

- **393

`ubuntu 16  默认安装了python3`

```

```

```
使用命令可测试 $ python3
```

``

`直接执行这两个命令即可：`

`sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 100`

`sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 150`