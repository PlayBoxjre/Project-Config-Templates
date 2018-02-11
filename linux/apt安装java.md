# Apt 安装java

## 1. 导入Webupd8 PPA

```bash
sudo add-apt-repository ppa:webupd8team/java

sudo apt-get update
```

## 2. 安装

```bash
sudo apt-get install oracle-java8-installer
```

选择确认条款，用Tab键切换。

## 3. 设置为默认jdk

```
//sudo update-java-alternatives -s java-8-oracle
sudo apt install oracle-java8-set-default
```

# 安装Java9

## 1. 导入Webupd8 PPA

```
sudo add-apt-repository -y ppa:webupd8team/java

sudo apt-get update
```

## 2. 安装

```
sudo apt-get install oracle-java9-installer
```

选择确认条款，用Tab键切换。

## 3. 设置环境变量

```
sudo apt-get install oracle-java9-set-default
```

## 4. 验证

```
ryan@ryan-900X5L:~$ java -version
java version "9"
Java(TM) SE Runtime Environment (build 9+181)
Java HotSpot(TM) 64-Bit Server VM (build 9+181, mixed mode)

```