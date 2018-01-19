---
layout: post
title:  "SpringBoot从入门到放弃"
date:   2017-08-10 08:14:54
categories: SpringBoot
tags:  SpringBoot
author: WHS
---

* content
{:toc}

[SpringBoot](http://projects.spring.io/spring-boot/)从入门到放弃




### 参考资料

* [SpringBoot官网](http://projects.spring.io/spring-boot/)

* [深入学习微框架：Spring Boot](http://www.infoq.com/cn/articles/microframeworks1-spring-boot)

* [为监控而生的数据库连接池！阿里云DRDS](https://github.com/alibaba/druid)


#### ORM

* [hibernate]()

* [JPA]()

* [MyBatis](https://github.com/mybatis/spring-boot-starter)
  

### 常用命令和方法

```
# 构建项目
gradle build
```
#### 程序部署

* War部署（传统方式）

将War文件拷贝到Tomcat Webapp目录下

* Jar部署

```
java -jar build/libs/gs-rest-service-0.1.0.jar
```
* 后台部署
通过jar部署后，关闭cmd窗口进程就停止了，因此需要让程序在后台运行

###### Windows环境下
1. 编写bat命令
2. 安装AlwaysUp软件，如下图所示

![](https://pic4.zhimg.com/50/v2-8c9d646fd06e7b656704fb42b85ba247_hd.jpg)

3. 打开AlwaysUp点击工具栏的第一个按钮，如下图所示，选择上面编写的bat文件，并填写服务名称。

![](https://pic2.zhimg.com/50/v2-730bd4126c6c3066e649b78638550c3f_hd.jpg)

完成了创建之后，在列表中可以看到我们配置的服务，通过右键选择Start xxx就能在后台将该应用启动起来了。

###### Linux/Unix环境下

####### nohup和Shell

该方法主要通过使用nohup命令来实现，该命令的详细介绍如下：

>nohup 命令

用途：不挂断地运行命令。

语法：nohup Command [ Arg … ][ & ]

描述：nohup 命令运行由 Command 参数和任何相关的 Arg 参数指定的命令，忽略所有挂断（SIGHUP）信号。在注销后使用 nohup 命令运行后台中的程序。要运行后台中的 nohup 命令，添加 &到命令的尾部。

所以，我们只需要使用nohup java -jar yourapp.jar &命令，就能让yourapp.jar在后台运行了。但是，为了方便管理，我们还可以通过Shell来编写一些用于启动应用的脚本，比如下面几个：

* 关闭应用的脚本：stop.sh

```
#!/bin/bash  
PID=$(ps -ef | grep yourapp.jar | grep -v grep | awk '{ print $2 }')  
if [ -z "$PID" ]  
then  
    echo Application is already stopped  
else  
    echo kill $PID  
    kill $PID  
fi  
```

* 启动应用的脚本：start.sh

```

#!/bin/bash  
nohup java -jar yourapp.jar --server.port=8888 &  
```

* 整合了关闭和启动的脚本：run.sh，由于会先执行关闭应用，然后再启动应用，这样不会引起端口冲突等问题，适合在持续集成系统中进行反复调用。

```
#!/bin/bash  
echo stop application  
source stop.sh  
echo start application  
source start.sh  
```
####### 系统服务



### 常见问题及解决方法

**问题描述**

```
druid 连接池 {dataSource-1} inited 是卡住
```

**解决办法**

1.重启IDEA或者重启电脑（可能是Druid bug 导致）

**问题描述**
```
Unregistering JMX-exposed beans on shutdown

```
**解决办法**

* 配置文件增加
```
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
```






