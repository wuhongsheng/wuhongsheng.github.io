---
layout: post
title:  "AndroidStudio手动下载配置Gradle"
date:   2018-03-07 08:14:54
categories: Android
tags:  AS Gradle
author: WHS
---

* content
{:toc}

 相信使用AS的童鞋都会遇到这个问题，每次更新项目Gradle版本的时候，AS下载非常缓慢，有时候点停止下载还会造成AS卡死。对于这个问题大致有以下三种解决方法
  



* [Gradle下载地址](http://services.gradle.org/distributions/)


1. 使用VPN下载

   我就是通过这种方式下载的，需要注意的是，要先开VPN然后再打开AS下载。



2. 手动下载配置

   [参考文章](https://blog.csdn.net/fuchaosz/article/details/51567808)

   既然有大神写了，而且比较详细，我就不再班门弄斧了。


3. 使用国内镜像地址

   使用阿里云的国内镜像仓库地址，就可以快速的下载需要的文件修改项目根目录下的文件 build.gradle ：

```
buildscript {
    repositories {
        maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    }
}

allprojects {
    repositories {
        maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    }
}
```

然后重新构建项目就可以了

* Gradle常用命令

```
# 查看项目依赖
gradle :app:dependencies
```


* [Android Gradle插件版本升级](https://developer.android.google.cn/studio/releases/gradle-plugin#revisions)


