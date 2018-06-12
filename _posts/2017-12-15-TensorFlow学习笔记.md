---
layout: post
title:  "TensorFlow学习笔记"
date:   2017-12-15 08:14:54
categories: AI
tags: AI TensorFlow
author: WHS
---

* content
{:toc}

[TensorFlow™](https://tensorflow.google.cn/)是一个使用数据流图进行数值计算的开源软件库。图中的节点代表数学运算， 而图中的边则代表在这些节点之间传递的多维数组（张量）。这种灵活的架构可让您使用一个 API 将计算工作部署到桌面设备、服务器或者移动设备中的一个或多个 CPU 或 GPU。 TensorFlow 最初是由 Google 机器智能研究部门的 Google Brain 团队中的研究人员和工程师开发的，用于进行机器学习和深度神经网络研究， 但它是一个非常基础的系统，因此也可以应用于众多其他领域。






### 基本概念

以常见的分类问题来介绍机器学习的基本概念

* 模型是特征列与标签的对应关系

* 样本是由特征列和标签组成的数据



### 分类

* [监督式机器学习监](https://developers.google.cn/machine-learning/glossary/#supervised_machine_learning)

* [非监督式学习](https://developers.google.cn/machine-learning/glossary/#unsupervised_machine_learning)


### 使用入门

* [官方教程](https://tensorflow.google.cn/get_started/)


### 常见的七个步骤

1. 搜集数据

2. 数据准备

3. 选择模型

4. 训练模型

5. 评估模型

推荐训练和评估所用的数据比例是4:1或7:3。选取何种比例取决于原始数据集的规模。如果你的数据非常多，那么用于验证的数据可能就不需要那么多了。

6. 参数微调

7. 预测

### 常见应用场景

* [基于TensorflowLite在移动端实现人声识别](http://www.infoq.com/cn/articles/speaker-dentification-based-on-tensorflowlite?utm_source=articles_about_mobile&utm_medium=link&utm_campaign=mobile)


### 相关教程

* [TensorFlow Codelabs](https://codelabs.tensorflowers.cn/)


