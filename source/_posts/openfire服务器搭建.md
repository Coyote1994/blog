---
title: openfire服务器搭建
date: 2018-12-30 22:03:18
categories: 
- 服务器
tags:
- openfire
- spark
---


### 前言

**关于环境**

macOS 10.14.2
JDK 1.8.0_191

**关于版本**

Openfire 4.2.3
Spark 2.8.3
XAMPP-VM(PHP 7.3.0)

`注意：亲测，JDK1.9、JDK1.11两个版本运行Spark2.8.3连接openfire时有bug！`

### Openfire的安装

在[oracle官网](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载JDK，安装

在[Ignite Realtime开源社区官网](https://igniterealtime.org/downloads/index.jsp)下载Openfire，安装

修改Openfire中的bin目录权限为允许读写

```
sudo chmod -R 777 /usr/local/openfire/bin
```

配置java环境变量，依次执行以下命令

```
sudo su

cd /usr/local/openfire/bin

export JAVA_HOME=`/usr/libexec/java_home`

echo $JAVA_HOME /Library/Java/JavaVirtualMachines/jdk1.8.0_191.jdk/Contents/Home
```

### 启动Openfire

**方式一：**依次执行以下命令

```
cd /usr/local/openfire/bin

./openfire.sh
```

**方式二：**在偏好设置的图形界面中star启动

### XAMPP安装

XAMPP快捷地为我们提供Apache服务器和MySQL数据库

在[XAMPP官网](https://www.apachefriends.org/index.html)下载软件，安装

start启动软件，start all再启动MySQL、Apache、ProFTPD

`注意：如果本地的MySQL正在运行，关闭本地MySQL，否则XMAPP中的MySQL无法启动`

### 使用PHPmyadmin管理Openfire中自带的MySQL数据库

XAMPP图形界面中，点击 Go To Application，选择PHPmyadmin，进入数据库管理页面，新建一个名为openfire的数据库（数据库名称可以随意，但是要小写，记住数据库的用户名和密码，配置openfire的时候会用）。

在PHPmyadmin管理界面，选择 导入->浏览，将 user/local/openfire/resources/database 路径下的 openfire_mysql.sql 文件导入到我们的数据库中，最后点击“执行”即可。

`提示：在简介中检查openfire文件的权限是否为读写`

### 配置Openfire

在偏好设置的图形界面中点击 Open Admin Console，进入配置页面。

三点需要注意：

* “域”是我们以后要连接的地址，这个可以选择默认也可以定义一个ip地址

* 数据类型选择标准数据库连接，数据库驱动选项为MySQL，数据库URL中替换[host-name]为XAMPP图形界面中的IP Address，[data-base-name]替换为在PHPmyadmin中创建的数据库名称，用户名和密码为创建时设置的用户名和密码
 
* 管理员账户如果不进行设置，默认为：用户名admin、密码admin

### Spark

安装方式：

* 在[Ignite Realtime开源社区官网](https://igniterealtime.org/downloads/index.jsp)下载安装包
* 在[github](https://github.com/igniterealtime/Spark/releases)下载安装包
* 在[github](https://github.com/igniterealtime/Spark)下载源代码，使用IDE编译

`提示：如果想要安装安插，注意版本选择`

登录可选择使用admin账户，也可在openfire中创建新账户。

可安装两个Spark进行通信测试，如果无法安装两个，可以另行安装Adium进行测试。


