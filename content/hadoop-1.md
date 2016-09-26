+++
Categories = ["Development", "GoLang"]
Description = ""
Tags = ["Development", "Hadoop", "Eclipse"]
date = "2016-06-13T20:45:25+08:00"
menu = ""
title = "Learning in Hadoop - Day 1"

+++

# 
第一次玩Hadoop

### 
最近在折腾机器学习，因为查阅到Yahoo的

>《Predicting The Next App That You Are Going To Use》

### 
这一篇Paper的时候，它提到了Google的 **MapReduce**以及 **Word2Vec** 。相信折腾过机器学习的小伙伴都会比较熟悉这两个东西了。于是，为了更深入地进行学习，我便无情地掉进了这个 **坑** 里。

### 
首先介绍一下背景，Yahoo的这篇Paper主要就是根据用户日常APP的使用习惯，然后对用户下一启动的APP进行预测。因为Yahoo认为日常手机的使用场景会对哪个APP的开启与否有着很强的关联性，于是他们便使用了 **Word2Vec** 对用户手机中记录的6个手机事件：

1. *Last Location Update*
2. *Last Charge Cavle*
3. *Last Audio Cable*
4. *Last Context Trigger*
5. *Last Context Pulled*
6. *Last App Open*

### 
进行计算词向量，用于文本预测。而Word2Vec有着3个广为流传的版本：

1. C
2. Python
3. Java

### 
但是这3个版本对于Yahoo来说性能都是不足的，经我测试，一个800M的文本在C语言版本中计算时间需要20Min！

### 
而在预测下一个APP这个场景里，这种计算速度是完全不可以接受的，于是Yahoo他们利用MapReduce重写了一个Word2Vec，将这个版本放在云端进行计算。这就是我进行MapReduce学习的原因。


