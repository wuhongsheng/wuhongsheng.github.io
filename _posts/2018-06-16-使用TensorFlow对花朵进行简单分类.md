---
layout: post
title:  "使用TensorFlow对花朵进行简单分类"
date:   2018-06-12 08:14:54
categories: AI
tags: AI TensorFlow
author: WHS
---

* content
{:toc}

我们将使用转移学习，这意味着我们从一个已经接受另一个问题培训的模型开始。然后我们将重新训练类似的问题。从头开始深入学习可能需要几天时间，但可以在短时间内完成转移学习。

我们将使用在ImageNet大型视觉识别挑战数据集上训练的模型。这些模型可以区分达尔马提亚或洗碗机等1,000个不同的类别。您将可以选择模型架构，因此您可以确定问题的速度，大小和准确性之间的正确折中。

我们将使用这个相同的模型，但重新训练它可以根据我们自己的例子区分少数类。







### 主要步骤

* [安装TensorFlow](http://wuhongsheng.top/2017/12/15/TensorFlow%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE/)

* 克隆git存储库

此代码中使用的所有代码都包含在此git存储库中。克隆存储库并cd放入其中。这是我们将要工作的地方。

```
git clone https://github.com/googlecodelabs/tensorflow-for-poets-2

cd tensorflow-for-poets-2
```

* 下载训练图像

在开始任何培训之前，您需要一组图像来教授模型有关您想要识别的新课程。我们创建了创作共用许可花卉照片的档案，以供初次使用。通过调用以下两个命令下载照片（218 MB）：

```
curl http://download.tensorflow.org/example_images/flower_photos.tgz \
    | tar xz -C tf_files
```

你现在应该有一个花卉照片的副本。通过发出以下命令来确认工作目录的内容：
```
ls tf_files / flower_photos

daisy/
dandelion/
roses/
sunflowers/
tulip/
LICENSE.txt
```
* 再训练网络

**配置你的MobileNet**

在这个练习中，我们将重新培训一个MobileNet。MobileNet是一个小型高效卷积神经网络。“卷积”意味着在图像中的每个位置执行相同的计算。

MobileNet可以通过两种方式进行配置：

输入图像分辨率：128,160,192或224px。毫不奇怪，摄入更高分辨率的图像需要更多的处理时间，但会导致更好的分类准确性。
模型的相对大小作为最大的MobileNet的一小部分：1.0,0.75,0.50或0.25。
这个codelab我们将使用224 0.5。

使用推荐的设置，通常只需几分钟即可在笔记本电脑上进行再培训。您将在Linux shell变量中传递设置。在shell中设置这些变量：

```
IMAGE_SIZE=224
ARCHITECTURE="mobilenet_0.50_${IMAGE_SIZE}"

# 查看变量
echo $IMAGE_SIZE
224

# 通过发出以下命令来激活该 conda 环境
source activate tensorflow
```
**启动TensorBoard**

在开始训练之前，tensorboard在后台启动。TensorBoard是一种包含在tensorflow中的监测和检测工具。您将使用它来监控培训进度。
```
tensorboard --logdir tf_files/training_summaries &

```
**查看再训练脚本**

重新训练脚本来自TensorFlow Hub回购，但不作为pip包的一部分进行安装。所以为了简单起见，我将它包含在codelab存储库中。您可以使用该python命令运行脚本。花一点时间浏览一下“帮助”。

```
python -m scripts.retrain -h

```

**运行训练脚本**

如引言中所述，ImageNet模型是具有数百万个参数的网络，可以区分大量的类。我们只训练该网络的最后一层，因此训练将在合理的时间内结束。

用一个大命令开始你的再训练（注意--summaries_dir选项，发送训练进度报告到tensorboard监测的目录）：

```
python -m scripts.retrain \
  --bottleneck_dir = tf_files / bottlenecks \
  --how_many_training_steps = 500 \
  --model_dir = tf_files / models / \
  --summaries_dir = tf_files / training_summaries /“$ {ARCHITECTURE}”\
  --output_graph = tf_files / retrained_graph.pb \
  --output_labels = tf_files / retrained_labels.txt \
  --architecture =“$ {ARCHITECTURE}”\
  --image_dir = tf_files / flower_photos
```

请注意，此步骤需要一段时间。

该脚本下载预先训练好的模型，添加一个新的最终图层，并在您下载的花卉照片上训练该图层。 

ImageNet不包括我们在这里培训的任何这些花卉物种。但是，使ImageNet可以区分1,000个类别的信息种类对区分其他对象也很有用。通过使用这个预先训练的网络，我们将这些信息用作最终分类层的输入，以区分我们的花类。

* 使用再训练模型

再训练脚本将数据写入以下两个文件：

tf_files/retrained_graph.pb，其中包含所选网络的一个版本，并在您的类别上重新培训最后一层。
tf_files/retrained_labels.txt，这是一个包含标签的文本文件。

**分类图像**

codelab repo还包含tensorflow的label_image.py示例的副本，您可以使用它来测试您的网络。花一点时间阅读此脚本的帮助：
```
python -m scripts.label_image -h

```
现在，让我们在雏菊的这个图像上运行脚本：

```
python -m scripts.label_image \
    --graph=tf_files/retrained_graph.pb  \
    --image=tf_files/flower_photos/daisy/21652746_cc379e0eea_m.jpg
```
每次执行都会打印出花标的列表，在大多数情况下，正确的花在顶部（尽管每个重新训练的模型可能略有不同）。

你可能会得到这样的雏菊照片的结果：
```
daisy (score = 0.99071)
sunflowers (score = 0.00595)
dandelion (score = 0.00252)
roses (score = 0.00049)
tulips (score = 0.00032)
```

* 可选训练参数

```
--learning_rate 
#参数控制训练期间最后一层更新的大小(比如0.005，训练需要更长的时间，但总体精度可能会增加。值越高learning_rate，比如1.0，可以训练速度更快，但通常会降低精度，甚至使培训不稳定。)

--summaries_dir 
#是控制张量板中名称的选项。之前我们使用过：
```

* 根据自己数据进行训练


看到脚本处理花图像后，您可以开始观察网络教学以识别不同的类别。

从理论上讲，你需要做的就是运行该工具，指定一组特定的子文件夹。每个子文件夹都按照您的某个类别命名，并仅包含该类别的图像。

如果您完成此步骤并传递子目录的根文件夹作为参数的--image_dir参数，那么脚本应该训练您提供的图像，就像它为花一样。

分类脚本使用文件夹名称作为标签名称，每个文件夹内的图像应该是与该标签对应的图片，如花卉档案中所示：

![](https://codelabs.tensorflowers.cn/codelabs/tensorflow-for-poets/img/9444bbae4d5d9ab1.png)

### 下一步

恭喜，您迈出了深入学习的更大的世界！

您可以在[TensorFlow网站](https://tensorflow.google.cn/)或TensorFlow GitHub [项目](https://github.com/tensorflow/)中查看有关使用TensorFlow的更多信息。TensorFlow还有许多其他资源，包括[讨论组](https://groups.google.com/a/tensorflow.org/forum/#!forum/discuss)和[白皮书](https://www.tfimgs.cn/resources/pdfs/45166.pdf)。

如果您有兴趣在移动设备上运行TensorFlow，请尝试本教程的第二部分：有三个版本：

[TFLite Android](https://codelabs.tensorflowers.cn/codelabs/tensorflow-for-poets-2-tflite/index.html#)
[TFLite iOS](https://codelabs.tensorflowers.cn/codelabs/tensorflow-for-poets-2-ios/index.html#0)
[TFMobile Android](https://codelabs.tensorflowers.cn/codelabs/tensorflow-for-poets-2/index.html#0)


或者去[TensorFlow游乐场](http://playground.tensorflow.org/#activation=tanh&batchSize=10&dataset=circle&regDataset=reg-plane&learningRate=0.03&regularizationRate=0&noise=0&networkShape=4,2&seed=0.74359&showTestData=false&discretize=false&percTrainData=50&x=true&y=true&xTimesY=false&xSquared=false&ySquared=false&cosX=false&sinX=false&cosY=false&sinY=false&collectStats=false&problem=classification&initZero=false&hideText=false)玩一下吧！


### 常见问题及解决方法

**问题**
```
InvalidArgumentError (see above for traceback): Expected image (JPEG, PNG, or GIF), got unknown format starting with '\000\005\026\007\000\002\000\000Mac OS X'
```

**解决方法**




### 参考

* [TensorFlow Codelabs](https://codelabs.tensorflowers.cn/codelabs/tensorflow-for-poets/index.html#0)

* [retraining tutorial](https://tensorflow.google.cn/tutorials/image_retraining)

* [Pete Warden's TensorFlow for Poets blog](https://petewarden.com/2016/02/28/tensorflow-for-poets/)

