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
1. 远程监督（DA）：基础分词器（embedding -> GCN -> CRF）+ 领域特定词挖掘器（互信息MI+熵ES+tfidf）-> 逻辑回归判断一个词是否是合法的词
2. 对抗训练（AT）：判别器的目的是区分一个输入S所属的域，两个损失如下，在训练中按照训练step交叉进行。

$$
\begin{aligned} \mathcal{L}_{d}=&-\mathbb{E}_{s \sim p_{\mathcal{S}}(s)}\left[\log G_{y}\left(E_{s h r}(s)\right]\right.\\ &-\mathbb{E}_{s \sim p_{\mathcal{T}}(s)}\left[\log \left(1-G_{y}\left(E_{s h r}(s)\right)\right]\right.\end{aligned}
$$

$$
\begin{aligned}
\mathcal{L}_{c}=&-\mathbb{E}_{s \sim p_{\mathcal{S}}(s)}\left[\log \left(1-G_{y}\left(E_{s h r}(s)\right]\right)\right.\\
&-\mathbb{E}_{s \sim p_{\mathcal{T}}(s)}\left[\log G_{y}\left(E_{s h r}(s)\right]\right.
\end{aligned}
$$

作者将新词发现常用的基本方法都使用上了，创新点在于远程监督和对抗训练

## Evaluation
以新闻领域语料PKU数据集作为源数据，目标领域有小说领域：斗罗大陆（DL），凡人修仙传（FR），诛仙（ZX），科学领域：皮肤病DM，专利PT。

对比方法：
1. Partial CRF
2. CWS-DICT：BiLSTM-CRF 结构，将词典信息融入网络中。
3. WEBCWS
4. baseline：GCNN，采用CRF解码
5. GCNN (Target)：在目标数据上只使用远程监督方法
6. GCNN (Mix)：将训练数据和目标数据（远程监督）混合
7. DA：是PKU和目标数据领域词的联合
8. AT:在目标数据上不使用远程监督，仅使用原始文本进行对抗训练。


实验说理部分可以进行学习

## Conclusion


作者结合远程监督和对抗训练来完成分词任务，远程监督有效的解决了OOV问题（strong conclusions），对抗训练来获取领域信息（weak conclusions）。



## Notes(optional) 
不在以上列表中，但需要特别记录的笔记。

## References(optional) 
列出相关性高的文献，以便之后可以继续track下去。

