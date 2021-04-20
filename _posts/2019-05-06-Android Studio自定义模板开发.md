---
layout: post
title:  "Android Studio自定义模板开发"
date:   2019-05-06 08:14:54
categories: AS
tags:  AS Android
author: WHS
---


* content
{:toc}

Android Studio提供的代码模板可帮助我们减少重复编写同一段代码的负担，而且可以遵循优化后的设计和标准。AS采用的是[Apache FreeMarker](https://freemarker.apache.org/)模板引擎。 





## 模板文件简介

Android Studio 的模板包括以下三种，这里重点讲的是第三种 Multi Template。在阅读本文之前，默认你是了解该模板的，知道为什么要使用它，如果不清楚它是什么，可以在网上查阅相关资料，当然也可以直接点击这里查看一下同行们对它的评价

* Live Template：代码片段级别
* File Template：单文件级别
* Multi Template：多个文件级别（以下称 Android Studio Template）

模板文件存放在...\Android Studio\plugins\android\lib\templates下

模板文件后缀名都是以【.ftl】结尾。

* globals.xml.ftl 全局变量文件 存放的是一些全局变量
* recipe.xml.ftl 配置要引用的模板路径以及生成文件的路径
* template.xml 模板的配置信息,以及要输入的参数.定义了模板的流程框架 基本结构
* template_blank_activity.png 显示的缩略图（只是展示用）
* LinActivity.java.ftl Activity模板文件


## 参数配置

* id ：通过该标识，获取输入的内容$(activityClass)
* name：输入框左边的提示语
* type : 输入值类型 可以为string或者boolean
* constraints：填写值的约束，比如不能为空，是否唯一
* suggest：建议值
* default:默认值
* help:输入框获取焦点时底部显示的提示语

## 参考资料

[Android Studio Template(模板)开发](https://www.jianshu.com/p/e3548f441440)

[Android Template使用](https://www.jianshu.com/p/defe731151bf)