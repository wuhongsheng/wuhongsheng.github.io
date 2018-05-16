---
layout: post
title:  "Android APK打包技巧"
date:   2018-03-07 08:14:54
categories: Android
tags:  AS Gradle
author: WHS
---

* content
{:toc}

 相信使用AS的童鞋都会遇到这个问题，每次更新项目Gradle版本的时候，AS下载非常缓慢，有时候点停止下载还会造成AS卡死。对于这个问题大致有以下三种解决方法
  





#### APK拆分

通过 APK 拆分，您可以高效地基于屏幕密度或 ABI 创建多个 APK。 例如，您可以利用 APK 拆分创建单独的 hdpi 和 mdpi 版本应用，同时仍将它们视为一个变体，并允许其共享测试应用、javac、dx 和 ProGuard 设置。

如需了解有关使用 APK 拆分的详细信息，请参阅 [APK 拆分}(http://tools.android.com/tech-docs/new-build-system/user-guide/apk-splits).




