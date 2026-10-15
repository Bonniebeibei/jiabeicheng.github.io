---
title: "Nature Methods | Article  NicheTrans: spatial-aware cross-omics translation"
collection: paper_reading
category: ai4bio
permalink: /paper_reading/nichetrans
---

空间多组学为复杂的生物系统提供更深层次的见解，但是受限于现有生物技术，大规模的空间多组学测序比较困难。此外，单细胞的多组学翻译技术直接用在空间多组学任务上也有局限性。所以作者提出了 NicheTrans，一种结合了空间微环境的单细胞级别多组学翻译方法。

---

## Introduction

细胞的空间分布可以为研究细胞交互、组织结构与疾病机制提供更深刻的见解。但是，面对生物系统的复杂性，只看单细胞组学信息是不够的，而现有的空间多组学生物技术又存在着很多问题，难以广泛推广使用。然后作者提到了单细胞级别的多组学翻译方法，并且指出它们的问题在于没有考虑组织微环境，以及空间组学技术所带来的额外信息，比如 H&E。正是因为生物技术暂时解决不了，计算方法又没有合适的替代，作者才提出了空间上的多组学翻译方法 NicheTrans。

---

## Motivation

![NicheTrans Figure 1](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/NicheTrans-fig1.png)

**Fig. 1a**：作者发现相同转录状态不一定对应相同的另一组学状态。（转录组和蛋白组表达并不一定对应）

**Fig. 1b**：作者发现这种“一对多”现象在全局数据结构中普遍存在

**Fig. 1c**：指出邻近切片的形态信息也可能帮助翻译。（单细胞多组学翻译的第二种缺点）

---

## 模型架构图

![NicheTrans Figure 2](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/NicheTrans-fig2.png)

**Fig. 2a**：作者把中心细胞和邻居组织成一个 niche

**Fig. 2b**：Transformer 在 niche 内部建模细胞间依赖，从而通过 encoder 形成 embedding；空间位置编码和其他可选信息也会进入这个表示。

**Fig. 2c**：框架支持多种跨组学翻译（通用性）

**Fig. 2d**：预测出的第二模态可恢复真正的空间多组学结构（可以用于下游任务）。作者的主张不是只生成一个看起来像真的矩阵，而是：生成的目标模态应当补充输入组学缺失的信息，从而揭示单组学无法区分的空间区域。

**Fig. 2e**：模型还能反向解释跨组学关系（可解释性，主要是 IG 归因）

---

## 从空间转录组预测空间代谢组（Spot-level）

加入空间 niche 后，跨组学翻译确实比把每个 spot 独立处理更准确；H&E 还能进一步提供增益。

作者首先选择 SMA 小鼠脑数据，因为该数据在同一组织切片上测量了空间转录组、代谢组和 H&E，因此有真实目标模态可作为 ground truth。实验使用 6-OHDA 损伤模型模拟帕金森病相关改变。

### Fig. 3：刷点

![NicheTrans Figure 3](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/NicheTrans-fig3.png)

Fig3 是我们比较熟悉的刷点

**Fig. 3a**：说明数据如何产生和对齐

**Fig. 3b**：定义训练和测试任务

**Fig. 3c**：定性证明 NicheTrans 恢复了代谢物的真实空间分布

### Fig. 4：定性分析与作为下游模型输入

![NicheTrans Figure 4](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/NicheTrans-fig4.png)

Fig4 主要是定性分析，以及把 embedding 作为下游分析模型的输入

**Fig. 4a-c** 给出真实空间域；证明转录组单模态和代谢组单模态都不够；

**Fig. 4d** 证明 NicheTrans 生成的多组学能够补充互补信息；

**Fig. 4e** 量化空间域恢复优势；

**Fig. 4f-j** 选择 PD 相关代谢物；识别相关基因程序；验证其空间和病变侧表达模式；证明这些基因构成功能一致的神经信号通路。（一套流程：IG 分析 - 空间可视化 - 样本差异分析 - 富集分析）

---

## 从空间转录组预测空间蛋白组（Cell-level）

**NicheTrans 的优势并不限于代谢组或 spot 级数据，也适用于单细胞级稀疏病理蛋白预测，并具有跨个体和负对照泛化能力。**

### Fig. 5：跨个体 RNA → Aβ/p-tau

![NicheTrans Figure 5](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/NicheTrans-fig5.png)

**Fig. 5a-b** 建立单细胞级 AD 空间多组学数据；定义跨个体 RNA→Aβ/p-tau 任务；

**Fig. 5c–f** 给出细胞背景和真实病理分布；证明预测图案更接近真实；证明 AUC 更高；

**Fig. 5g-h** 建立 wild-type 负对照；这一步有意思，类似于空白对照组，证明 NicheTrans 不会严重过预测；

**Fig. 5i** 证明在类别不平衡下仍具有更好的敏感性、F1 和综合表现。

### Fig. 6：预测出的 Aβ 是否足以支持病理微环境发现

![NicheTrans Figure 6](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/NicheTrans-fig6.png)

主要是发现模型生成的 Aβ 位置周围确实富集着 Micro3–Astro3–Oligo1 组织结构。

以及测试了模型的归因功能，结果确实指向了小胶质细胞、Trem2/Apoe/Cst7 等基因和先天免疫通路。

---

## 学习点

作者的每一个观点都有多个子论点支撑，比如为了说明单细胞多组学翻译算法不好，作者能给出两点理由并且一一论证，给人非常充实的感觉。此外，作者虽然只做了两个例子（转录组-代谢组，转录组-蛋白组），但是每个例子都不重复：既有 spot-level 也有 cell-level，组学也不相同；有的刷点，有的强调可以作为下游方法的输入，有的考虑了负样本对照，有的结合具体场景分析了细胞组织结构。不过要证明的观点主要还是更准确、更还原真实数据、可以归因。

---

## 🔗 相关资源

* **论文原文**: [DOI: 10.1038/s41592-026-03153-3](https://doi.org/10.1038/s41592-026-03153-3)
