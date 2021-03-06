---
layout: post
title:  "ArcServer发布可同步地图服务"
date:   2018-03-07 08:14:54
categories: GIS
tags:  GIS ArcServer
author: WHS
---

* content
{:toc}

 从ArcGIS 10.2.1开始推出离在线一体化技术之后，数据的离在线一体化编辑一直是大家所关注的一个热点。数据存储在企业级地理数据库中，通过ArcGIS桌面软件加载后配图处理，并发布到ArcGIS for Server中，供移动端设备离线编辑使用，并可以同步回传版本化存档。这其中涉及多项配置操作，本篇文章主要针对FeatureService服务的发布流程做一个简单的介绍。以备查阅。
 ![](https://images2015.cnblogs.com/blog/412448/201609/412448-20160909164428254-500287581.png)
  






### 操作流程（以10.3ArcServer为例）

1.准备数据源（ArcSDE）->2.添加GlobalId->3.启用存档->4.制作地图文档(MXD)->5.新增数据库连接->6.添加Server连接->7.注册数据库到服务器->8.发布服务

1. 在CataLog中连接数据库、新建数据集、新建图层

![连接SDE](http://ooxw95lkz.bkt.clouddn.com/%E8%BF%9E%E6%8E%A5SDE.png)

![新建数据集](http://ooxw95lkz.bkt.clouddn.com/%E6%96%B0%E5%BB%BA%E8%A6%81%E7%B4%A0%E9%9B%86.png)

![新建要素类](http://ooxw95lkz.bkt.clouddn.com/%E6%96%B0%E5%BB%BA%E8%A6%81%E7%B4%A0%E7%B1%BB.png)

![添加字段](http://ooxw95lkz.bkt.clouddn.com/%E6%B7%BB%E5%8A%A0%E5%AD%97%E6%AE%B5.png)


2. 添加GlobalId 

![添加GlobalId](http://ooxw95lkz.bkt.clouddn.com/%E6%B7%BB%E5%8A%A0Globalid.png)

在增加GlobalId时可能会出现与数据锁冲突，如下图所示：执行SQL解除锁
   ```select * from sde.table_locks for update
   ```


3. 启用存档，这不用没问题

![启用存档](http://ooxw95lkz.bkt.clouddn.com/%E5%90%AF%E7%94%A8%E5%AD%98%E6%A1%A3.png)

4. 保存mxd文件

5. 新建数据库连接

6. 添加Server连接

7. 注册数据库到服务器

8. 发布地图服务

![发布地图服务](http://ooxw95lkz.bkt.clouddn.com/%E5%8F%91%E5%B8%83%E5%9C%B0%E5%9B%BE%E6%9C%8D%E5%8A%A1.png)

![开启同步功能](http://ooxw95lkz.bkt.clouddn.com/%E5%BC%80%E5%90%AF%E5%90%8C%E6%AD%A5%E5%8A%9F%E8%83%BD.jpg)

### 参考链接

[https://server.arcgis.com/zh-cn/server/latest/get-started/windows/tutorial-set-up-feature-service-data-for-offline-use.htm](https://server.arcgis.com/zh-cn/server/latest/get-started/windows/tutorial-set-up-feature-service-data-for-offline-use.htm)

[https://server.arcgis.com/zh-cn/server/latest/get-started/windows/tutorial-create-offline-maps-with-versioned-data.htm](https://server.arcgis.com/zh-cn/server/latest/get-started/windows/tutorial-create-offline-maps-with-versioned-data.htm)

[http://www.cnblogs.com/gis-luq/p/5857188.html](http://www.cnblogs.com/gis-luq/p/5857188.html)







