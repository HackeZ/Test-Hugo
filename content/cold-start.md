+++
Categories = ["Development", "UX"]
Description = "About Two Different Cold Start"
Tags = ["Development", "Cold Start"]
date = "2016-07-13T19:39:47+08:00"
menu = ""
title = "Cold Start"

+++

# About Two Different Cold Start

## APP Cold Start
-------

## **What's App Cold Start**

> 对于最近安装的App a，我们不可能得到该对于该App用户的特殊使用信息。尤其是打开该App的概率 P(a)。另一方面，对于给定的 feature（特征）的先验概率（指根据以往经验和分析得到的概率），可以从其他用户的信息中获取。

> 因此，对于最近安装的App，怎么估计其打开概率P(a)是至关重要的。


## **Yahoo's Experimental Result**

通过记录最近安装的App打开记录（活跃度），图5（Daily）以及图6（Hourly）：

![Days After Installation](http://7xsxev.com1.z0.glb.clouddn.com/Days-after-installation.png)
### 
图 5  日常App安装后的活跃度

![Hours After Installation](http://7xsxev.com1.z0.glb.clouddn.com/Days-after-installation.png)
### 
图 6  每小时App安装后的活跃度
 
从图 5 和 6 中可以看到，最近安装的App一个显着的特点是在安装后的数小时内非常活跃。但是经过这段时间之后，最近安装的App 的活跃度显着减少。与此相反，一些一开始活跃度并不高的 App 经常在它们安装后的很长一段时间依然在使用。

## **To Solve App Cold Start**

因此，为了更好地获取最近安装App打开频率，我们根据它们的活跃度持续时间长短，定义两种App类型，分别为：

1. Short-term （活跃度持续时间短，在刚开始的一段时间活跃度很高）
2. Long-term（活跃度持续时间长，在刚开始的一段时间活跃度不够高）
 

为捕抓每个App在时间上的突出显著性，我们将App使用数据转化为 **Beta(α,β)** 值，为了区分时间显著性，我们使用尖峰（excess kurtosis）δ来评价每个App的时间使用峰度：
![Excess Kurtosis Expression](http://7xsxev.com1.z0.glb.clouddn.com/Excess-kurtosis-expression.png)


通过尖峰值可以判断最近安装App的类型：

- 一个高尖峰值的App意味着它越有可能是Short-term类App（Game）
- 一个低尖峰值的App意味着它越有可能是Long-term类App（Communication）
 

**Short-term类的App可以通过获取特定的特点的用户来获取平均打开频率。**      
**Long-term类的App则可以通过获取所有用户的平均值。**

随着用户打开App的事件增加，我们可以计算最近安装App的打开概率，因此，我们使用贝叶斯平均其他用户的历史信息来计算接下来的使用信息。计算最近安装App的打开概率公式如下：

![App Cold Start Expression](http://7xsxev.com1.z0.glb.clouddn.com/App-cold-start-expression.png)


通过计算公式，仅仅通过少量的用户打开App事件得到同一App的其他用户近似的非加权值的启动概率。**用户打开App事件越多，该公式的准确率越高。**

------

## User Cold Start

## **What's User Cold Start**

> 在这一小节，我们提出两种方法解决User冷启动问题，什么是User冷启动呢？
> 当一个用户安装了一个Launcher软件（Aviate，Buzz，Go 等桌面软件），我们在不知道这个用户的任何信息下如何向该用户推荐App清单呢？这个就是User冷启动问题。


## **Two Ways to Solve**

1. 最相似用户策略：
当这个new User在安装了Launcher之后，我们可以在已知的用户集中找到跟他最相似的用户，并将这个用户的使用指标赋值给他。    
那么怎么去计算跟他最相似的用户呢，我们可以使用 **Jaccard系数** 进行计算，这个系数主要用来比较样本集中的相似性和分散性的一个概率。    
**计算出用户之间的相似度，就可以将最相似用户的App清单进行推送。**     
事实上，最相似的用户的App清单与新用户的清单还是有很大的不一样的，在极端条件下，也就是不涉及到敏感的用户信息条件下，他们之间的App清单相似度甚至不会超过一个 **纯粹随机策略** （相当于“猴子排序”）。    
虽然这个策略提高了User冷启动的平均准确度，但是也限制了可生成用户建议数的范围。    

2. 伪用户策略：
通过生成“伪历史”（假的用户使用记录）可以解决用户冷启动问题，而且该策略可以作为新用户训练PTAN模型的一种方法。    
这个想法在于找到少量的相似用户，其App清单能够覆盖新用户的App清单。这是一个简单的 **NP-Hard证明问题** 。

> P.S 1: NP-Hard问题也就是不能在限定的时间内计算出结果的问题，只能通过候选答案来验证这个答案是不是我们已知问题的一个答案

> P.S 2关于这个问题为什么是一个NP-Hard问题，论文中没有给出解释，如果想了解怎么判断一个问题是不是NP-Hard问题，可以到 [这里](http://blog.csdn.net/com_stu_zhang/article/details/7248277) 查看解释，因为解释非常复杂，请允许我不复制粘贴上来

通过算法5，我们生成了伪用户数据：
![Build Pseudo User](http://7xsxev.com1.z0.glb.clouddn.com/Build-pseudo-user.png)