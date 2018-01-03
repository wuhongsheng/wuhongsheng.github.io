---
layout: post
title:  "TensorFlow从入门到精通"
date:   2017-12-15 08:14:54
categories: AI
tags: AI
author: WHS
---

* content
{:toc}

[TensorFlow™](https://tensorflow.google.cn/)是一个使用数据流图进行数值计算的开源软件库。图中的节点代表数学运算， 而图中的边则代表在这些节点之间传递的多维数组（张量）。这种灵活的架构可让您使用一个 API 将计算工作部署到桌面设备、服务器或者移动设备中的一个或多个 CPU 或 GPU。 TensorFlow 最初是由 Google 机器智能研究部门的 Google Brain 团队中的研究人员和工程师开发的，用于进行机器学习和深度神经网络研究， 但它是一个非常基础的系统，因此也可以应用于众多其他领域。

### 安装

您必须选择安装TensorFlow的机制。支持的选择如下：
* virtualenv （官方推荐）
* "native" pip
* Docker
* installing from sources, which is documented in a separate guide.

### 常用命令

```
# 启用
$ source ~/tensorflow/bin/activate      # bash, sh, ksh, or zsh
$ source ~/tensorflow/bin/activate.csh  # csh or tcsh 
(tensorflow)$                           # 启用成功
# 关闭
(tensorflow)$ deactivate 
```

### 常见问题

**问题描述**

```
/Volumes/Transcend/TensorFlow/lib/python3.6/importlib/_bootstrap.py:219: RuntimeWarning: compiletime version 3.5 of module 'tensorflow.python.framework.fast_tensor_util' does not match runtime version 3.6
```

**原因及解决办法**

Python版本不匹配
