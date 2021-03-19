---
layout: post
title:  "TensorFlow环境配置"
date:   2017-12-15 08:14:54
categories: AI
tags: AI TensorFlow
author: WHS
---

* content
{:toc}

[TensorFlow™](https://tensorflow.google.cn/)是一个使用数据流图进行数值计算的开源软件库。图中的节点代表数学运算， 而图中的边则代表在这些节点之间传递的多维数组（张量）。这种灵活的架构可让您使用一个 API 将计算工作部署到桌面设备、服务器或者移动设备中的一个或多个 CPU 或 GPU。 TensorFlow 最初是由 Google 机器智能研究部门的 Google Brain 团队中的研究人员和工程师开发的，用于进行机器学习和深度神经网络研究， 但它是一个非常基础的系统，因此也可以应用于众多其他领域。








### [安装（以MacOS为例）](https://tensorflow.google.cn/install/install_mac#ValidateYourInstallation)

您必须选择安装TensorFlow的机制。支持的选择如下：
* virtualenv （官方推荐）

  1. 启用终端
  2. 通过以下命令安装pip 和 Virtualenv：

  ```
   $ sudo easy_install pip
   $ pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --upgrade virtualenv 

  ```

  3. 通过使用以下某种格式发出命令来创建一个 Virtualenv 环境：
  
  ```
   $ virtualenv --system-site-packages targetDirectory # for Python 2.7
   $ virtualenv --system-site-packages -p python3 targetDirectory # for Python 3.n
  ```

* "native" pip
* Docker
* installing from sources, which is documented in a separate guide.



### 常用命令

```
# 启用(新的 shell 中使用 TensorFlow 时，您都必须激活 Virtualenv 环境)
$ cd 
$ source ~/tensorflow/bin/activate      # bash, sh, ksh, or zsh
$ source ~/tensorflow/bin/activate.csh  # csh or tcsh 
(tensorflow)$                           # 启用成功

# 通过发出以下命令来激活该 conda 环境
source activate tensorflow

# 关闭
(tensorflow)$ deactivate 
# 启动TensorBoard(在开始训练之前，tensorboard在后台启动。TensorBoard是一种包含在tensorflow中的监测和检测工具。您将使用它来监控训练进度)
tensorboard --logdir tf_files/training_summaries &

# 查看版本
python3 -c 'import tensorflow as tf; print(tf.__version__)'

# 升级TensorFlow
$ pip3 install --upgrade tensorflow -i https://mirrors.aliyun.com/pypi/simple
# 卸载TensorFlow
$ pip uninstall tensorflow
$ pip3 uninstall tensorflow 
```

### 常见问题

**问题描述**

```
/Volumes/Transcend/TensorFlow/lib/python3.6/importlib/_bootstrap.py:219: RuntimeWarning: compiletime version 3.5 of module 'tensorflow.python.framework.fast_tensor_util' does not match runtime version 3.6
```

**原因及解决办法**

Python版本不匹配

**问题描述**
```
>>> import tensorflow as tf
Traceback (most recent call last):
  File "/Users/whs/anaconda3/lib/python3.6/site-packages/tensorflow/python/pywrap_tensorflow_internal.py", line 18, in swig_import_helper
    fp, pathname, description = imp.find_module('_pywrap_tensorflow_internal', [dirname(__file__)])
  File "/Users/whs/anaconda3/lib/python3.6/imp.py", line 297, in find_module
    raise ImportError(_ERR_MSG.format(name), name=name)
ImportError: No module named '_pywrap_tensorflow_internal'
```
**原因及解决方法**
```
tensorflow 安装失败可能是pip版本的问题或者是网络问题引起多用 pip 命令安装几次就好
删除tensorflow 
rm -r ~/tensorflow
重新安装
 (targetDirectory)$ pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --upgrade tensorflow      # for Python 2.7

```





