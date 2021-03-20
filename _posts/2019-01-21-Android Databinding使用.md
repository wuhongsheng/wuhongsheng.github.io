---
layout: post
title:  "Android DataBinding使用"
date:   2019-01-21 08:14:54
categories: Android
tags:  Android Jetpack DataBinding
author: WHS
---

* content
{:toc}

[数据绑定库](https://developer.android.google.cn/topic/libraries/data-binding/)是一个支持库，允许您使用声明性格式而不是以编程方式将布局中的UI组件绑定到应用程序中的数据源。



### Databinding常见用法

1.字符串判空
```xml
<import type="android.text.TextUtils" />                
    <ImageView
 	isGone="@{TextUtils.isEmpty(viewModel.changeInfo)}"
    android:id="@+id/iv_clear"
    android:padding="5dp"
    android:layout_marginEnd="10dp"
    app:layout_constraintEnd_toEndOf="@id/et"
    app:layout_constraintTop_toTopOf="@+id/et"
    app:layout_constraintBottom_toBottomOf="@id/et"
    android:src="@mipmap/login_close"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"/>

```



### 常见问题

**错误描述**

```
java.lang.RuntimeException: Unable to start activity ComponentInfo{com.wantong.product/com.example.xuncha.ui.task.PatrolTaskDetialActivity}: java.lang.RuntimeException: view must have a tag
```

**原因及解决方法**

使用DataBinding的项目中存在同名布局文件，找到对应的生成文件查看具体错误原因


