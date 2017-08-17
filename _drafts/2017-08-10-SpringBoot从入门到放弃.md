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

* [深入学习微框架：Spring Boot](http://www.infoq.com/cn/articles/microframeworks1-spring-boot)

* [为监控而生的数据库连接池！阿里云DRDS](https://github.com/alibaba/druid)

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






