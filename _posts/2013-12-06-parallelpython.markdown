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

