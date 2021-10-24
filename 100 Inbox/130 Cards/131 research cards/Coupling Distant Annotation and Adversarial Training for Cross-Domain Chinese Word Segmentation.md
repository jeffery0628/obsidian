---
Create: 2021年 十月 24日, 星期日 16:51
Title:
Authors: [Ning Ding , Dingkun Long , Guangwei Xu  , Muhua Zhu , Pengjun Xie, Xiaobin Wang , Hai-Tao Zheng]
Groups: [Tsinghua University, China Alibaba Group]
tags: 
  - NLP/分词
  - 对抗训练
---

论文链接：[[700 Attachments/710 论文/Coupling Distant Annotation and Adversarial Training for Cross-Domain Chinese Word Segmentation.pdf]]



# Coupling Distant Annotation and Adversarial Training for Cross-Domain Chinese Word Segmentation
## Summary
写完笔记之后最后填，概述文章的内容，以后查阅笔记的时候先看这一段。注：写文章summary切记需要通过自己的思考，用自己的语言描述。忌讳直接Ctrl + c原文。

## Research Objective(s)
作者的研究目标 : 跨领域无监督 **分词**

## Background / Problem Statement
研究背景：基于监督学习的神经网络分词方法，在进行跨领域分词应用时，有两个问题：
1. 性能下降：训练样本和目标样本的数据分布不一样
2. OOV：目标样本中出现训练集中未出现过的词

### 流派：
1. 使用领域字典辅助分词
2. 利用目标领域无监督数据，来学习目标领域数据分布。


## Method(s)

![[700 Attachments/720 pictures/Pasted image 20211024172344.png]]
作者的对抗远程监督分词方法由两部分组成：
1. 远程监督（DA）：基础分词器（embedding -> GCN -> CRF）+ 领域特定词挖掘器
2. 对抗训练（AT）：


作者解决问题的方法/算法是什么？是否基于前人的方法？基于了哪些？

## Evaluation
作者如何评估自己的方法？实验的setup是什么样的？感兴趣实验数据和结果有哪些？有没有问题或者可以借鉴的地方？

## Conclusion
作者给出了哪些结论？哪些是strong conclusions, 哪些又是weak的conclusions（即作者并没有通过实验提供evidence，只在discussion中提到；或实验的数据并没有给出充分的evidence）?

## Notes(optional) 
不在以上列表中，但需要特别记录的笔记。

## References(optional) 
列出相关性高的文献，以便之后可以继续track下去。

