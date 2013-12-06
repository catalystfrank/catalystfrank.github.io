---
title: Python高性能计算
layout: post
guid: urn:uuid:b87da13a-a4dd-402f-b06a-cef7eeee2d85
tags:
  - 数据挖掘
  - 高性能计算
---

最近做文本挖掘，接触了Ansj分词的jar包（具体里面怎么写的我就不看了），
TF-IDF（Term Frequency-Inverse Document Frequency，这东西看名字就知道一个naive Bayes，唬不住人的），还有Word2Vec（Based on Deep Learning from Google）。
因为R性能太差直接换用Python。讽刺的是，Python尽管读取文本文件非常快，但是词频统计居然Stackless和Threading都是伪并行，只是使用不同核而已，不是用于并行计算设计。

我只想说，你他妈在逗我？

### Parallel Python

[这里](http://www.cnblogs.com/flyingis/archive/2009/11/12/1601574.html)是一个Parallel Python很好的例子。

因为就像submit job到服务器类似的写法，所以Single Machine Parallel也是能达到不错的性能的。

顺便一说，这样好像很容易搞坏工作机，最好还是不要在集群上试。

### 我的想法

Anaconda这公司开发的NumbaPro已经成为Cuda Python的标准了，而且Google在Deep Learning上的研究也显示，GPU计算是未来一个高性能计算的重要方向。

我的机器装的是Anaconda Python + PyCharm IDE, 还有个2.7.5的Stackless。Stackless写法很整洁，可惜不是真并行。

接下来这个方案：E5-2690v2 *2 + 128G 内存 + ThinkStation 机箱 + SuperMicro 主板 + GTX 780Ti，这是我现在能想到的高性能单机解决方案，只差钱了。

真正想用起来的话，等到Cuda Toolkit 6.0出来之后，对内存的管理更加简单和规范大家就可以放心用了。

我个人是从3.0开始用Cuda C++写GPU并行程序的，但是中间动荡了一段时间就给荒废了，希望最近能有机会捡起来。