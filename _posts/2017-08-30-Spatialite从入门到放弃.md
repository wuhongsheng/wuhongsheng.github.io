---
layout: post
title:  "Spatialite使用从入门到放弃"
date:   2017-08-30 08:14:54
categories: DB
tags:  Spatialite
author: WHS
---

* content
{:toc}

Sptialite使用从入门到放弃





### 参考资料

[官网](http://www.gaia-gis.it/gaia-sins/)

[spatialite-cookbook](http://www.gaia-gis.it/gaia-sins/spatialite-cookbook/index.html)

### 常用SQL语句()

CGCS2000 4490(国家2000坐标系SRID)

```
# 插入点数据
INSERT INTO photos(FK_FXH_UID,TIME,INFO,REMARK,ADDEESS,URI,Geometry) VALUES (1,'2017-08-30','INFO','REMARK','ADDRESS','URI',geomfromtext('POINT(117.34 32.832)',4490))

# 导出Shapfile
select ExportSHP('fxh','Geometry','Test','UTF-8','MULTIPOLYGON')
```






