---
layout: post
title:  "React Native从入门到放弃"
date:   2017-07-23 08:14:54
categories: 跨平台
tags:  RN 跨平台
author: WHS
---

* content
{:toc}

[React Native](http://facebook.github.io/react-native/) 开发指南




## 环境配置

1.安装node.js

2.安装react-native命令行工具

```
npm install -g react-native-cli
```

3.新建项目

```
react-native init [ProjectName]
```

## 常用命令


#### [升级新版本](http://reactnative.cn/docs/0.46/upgrading.html)

1. 安装Git并设置环境变量

2. 安装react-native-git-upgrade工具模块

```
$ npm install -g react-native-git-upgrade
```

3. 进入项目目录下运行更新命令

```
$ react-native-git-upgrade
# 这样会直接把react native升级到最新版本

# 或者是：

$ react-native-git-upgrade X.Y.Z
# 这样把react native升级到指定的X.Y.Z版本
```
#### 运行方法

```
To run your app on iOS:
   cd /Users/whs/AwesomeProject
   react-native run-ios
   - or -
   Open ios/AwesomeProject.xcodeproj in Xcode
   Hit the Run button
To run your app on Android:
   cd /Users/whs/AwesomeProject
   Have an Android emulator running (quickest way to get started), or a device connected
   react-native run-android
```


## 参考资料

1. [写给移动开发者的 React Native 指南](http://www.jianshu.com/p/b88944250b25)

2. [React Native 中文网](http://reactnative.cn/post/3634)


### 常见问题



**问题描述**
```
React Native unable to load script from assets index.android.bundle on windows
```
**解决办法**

1.在工程目录里建assets目录
2.运行命令
```
 react-native bundle --platform android --dev false --entry-file index.android.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
```
3.```react-native run android```




