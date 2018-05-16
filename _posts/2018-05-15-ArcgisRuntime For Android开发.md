---
layout: post
title:  "ArcgisRuntime For Android开发"
date:   2018-05-15 08:14:54
categories: GIS
tags:  GIS
author: WHS
---

* content
{:toc}

ArcgisRuntime For Android 开发




### 相关网站

* [OSGeo](http://www.osgeo.org/)

* [OGC](http://www.opengeospatial.org/)（开放地理空间联盟）
是一个致力于为全球地理空间社区制定优质开放标准的国际非营利组织。这些标准是通过协商一致的过程完成的，任何人都可以免费使用来改善世界地理空间数据的共享。



### 技术路线

#### 开源GIS

###### 相关组织

[OSGeo](https://github.com/OSGeo)

###### 技术路线
*  postgis+geoserver/geotools+ol/leaflet

###### 主要软件和工具

桌面GIS - QGIS，OpenEV

GIS工作站 - GRASS，OSSIM

GIS开发工具包 - GDAL / OGR，地理工具

地理统计 - GNU R

Web映射服务器 - MapGuide开源，Mapserver，GeoServer

网页地图客户端 - MapBuilder

元数据服务器 - Isite

空间数据引擎 - PostGIS，myGIS

C／S数据库 - PostgreSQL，mySQL，SQLite


	
##### 商业GIS

* Arcgis平台

* 超图平台

### 主要技术

#### 投影转换和坐标校正

* [PROJ](https://github.com/OSGeo/proj.4) 

* [Proj4J](http://trac.osgeo.org/proj4j/) JAVA Lib

#### 矢量切片

  1. Mapbox 
  2. Geoserver 
  3. titlecache工具可以切成geojson


#### 最短路径分析

* postgresql + postgis+ pgrouting



