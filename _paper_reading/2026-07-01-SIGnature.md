---
title: "Nature Biotechnology | Article  Scoring gene importance by interpreting single-cell foundation models"
collection: paper_reading
category: ai4bio
permalink: /paper_reading/signature
---

这篇文章聚焦于如何在特定细胞情境下量化一个基因的功能重要性。文章强调，绝对表达量并不是可靠指标；差异表达、signature scoring 等方法在跨实验泛化方面有限；而归一化/整合方法也难以有效应用到成千上万的公开单细胞实验中。

基于此，作者提出了 SIGnature，一个通过解释单细胞 foundation model 来为基因重要性打分的框架。它的核心思想是：如果一个基因对细胞在模型潜在空间中的位置贡献更大，那么这个基因就更能定义该细胞在当前情境下的功能状态。

---

## 图 1：通过归因分数量化基因重要性

![SIGnature Figure 1](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/SIGnature-fig1.png)

**Fig. 1a–c** 描述了归因分数量化基因重要性的核心思想。作者通过与视觉图像归因方法类比，说明如何衡量某个基因对细胞在模型潜在空间中位置的贡献，并据此判断该基因对该细胞表征的重要性。

**Fig. 1d** 概述了 attribution score 相比表达量的优势：SIGnature 能更好地区分生物信号与技术伪影，具有跨数据比较的鲁棒性，并能够学习到真实的生物信号。

**图块 1：证明归因分数可以挖掘更准确的重要基因。** 很多基因虽然表达量高，但更多反映的是高丰度、线粒体/核糖体/housekeeping 信号，不一定最能定义该细胞类型的功能身份。而 attribution 高的 BANK1、MS4A1、CD79A 等基因更符合 B cell marker。

**图块 2：证明归因分数更少受到技术伪影影响。** Attribution score 与测序深度、UMI、library complexity 等技术因素的相关性更低，因此更抗技术伪影。

**图块 3：证明归因分数可以支持跨研究 NMF 分析。** 作者对跨研究 T cell attribution matrix 做 NMF，得到 Treg-associated factor。该因子在多个组织中能稳定区分 Treg 与其他 CD4+ T cells，正文也提到 Treg factor 在 Treg 中的 usage score 显著高于其他 CD4+ T cells。

**图块 4：证明归因分数在细胞分类中效果更好。** 作者用找到的特征作为 logistic 细胞分类的输入，发现归因方法效果最好。

**Fig. 1e** 描述了 SIGnature 如何进行大规模信号挖掘。它的高效计算形式是：先预计算每个细胞、每个基因的 attribution score；当输入一个 gene signature 时，对 signature 内基因的 attribution 求平均，得到每个细胞的 mean attribution score；然后再看哪些细胞类型、样本或疾病中该 signature 富集。因此，它检索的是 gene signature / gene set 的激活。

---

## 图 2：不同模型和归因方法如何影响 attribution score 的质量

![SIGnature Figure 2](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/SIGnature-fig2.png)

Fig. 2 主要回答三个问题：用哪个 foundation model？用哪种 attribution 方法？得到的 attribution score 是否比表达量更稳健、更有生物学意义？

作者分别比较了不同模型、不同解释方法、不同评价指标，以及表达量基线与 shuffled gene baseline。这个结果部分的作用是把 SIGnature 从“一个直觉上合理的方法”推进到“一个经过系统选择和验证的计算流程”。

---

## 图 3：attribution 具有生物学意义、抗技术噪声，并支持跨研究 NMF

![SIGnature Figure 3](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/SIGnature-fig3.png)

Fig. 3 更像是 Fig. 1d 前三个图块的扩展，展开论证 attribution 的生物学意义、抗技术噪声和跨研究 NMF 能力。

**Fig. 3b–c：证明 attribution 高的基因更符合细胞身份相关基因。** 这对应 Fig. 1d 的 Relevant top genes。Attribution 高的基因更像“定义细胞身份的基因”，而不是单纯“表达量最高的基因”。

**Fig. 3d–f：证明 attribution 可以突出低表达但功能重要的 marker 基因和转录因子。** 作者进一步量化这个结论：marker 基因以及 transcription factor 通常表达量低，但功能重要。

**Fig. 3g–h：证明 attribution 比 expression 更少受到技术因素影响。** 这对应 Fig. 1d 的 Reduced technical artifacts。具体展示为：marker gene expression 和 total counts per cell 相关性很强，而 marker gene attribution 与 total counts 的相关性弱很多。

**Fig. 3i–k：证明 attribution-based NMF 可以发现可解释且可泛化的跨研究 gene program。** 这对应 Fig. 1d 的 NMF across studies。作者完整展示了跨研究 T cell 数据做 NMF 后，确实能得到 CD8+ T、cytokine response、Treg 等可解释因子，并且 Treg factor 可以泛化到多个组织中的 Treg 细胞。

---

## 图 4：mean attribution score 可以衡量 gene signature activation

![SIGnature Figure 4](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/SIGnature-fig4.png)

Fig. 4 展示 mean attribution score 在细胞身份识别上的应用，证明方法得到的 signature 确实学到了生物信号。

一个已知 gene set / signature 是否在某个细胞中“激活”，可以不再只看这些基因的表达量，而是看这些基因对该细胞 foundation model 表征的平均贡献，也就是 mean attribution score。

**Fig. 4a：证明 cell type marker signature 在对应细胞类型中得分最高。** 这是对方法直觉的第一层验证：cell type marker signature 的 mean attribution 在对应细胞类型中最高。

**Fig. 4b–d：证明 mean attribution score 比已有 signature scoring 方法表现更好。** 作者比较监督和无监督分类任务，发现 mean attribution score 整体表现最好。

**Fig. 4e–f：证明 mean attribution score 比传统表达打分更适合跨数据集比较。** 它可以正确区分不同研究里的 CD8+ T 和 CD4+ T；而 Scanpy 的表达打分会出现跨研究错判，把另一个研究里的 CD4+ T 打得比真正的 CD8+ T 还高。

**Fig. 4g–i：证明 mean attribution score 可以推广到更多研究和细胞类型。** Fig. 4g 显示，在多个数据集中，B cell marker 的 mean attribution score 在 B cell 及相关谱系细胞中更高，mast cell marker 的 mean attribution score 在 mast cell 中更高。Fig. 4h 和 Fig. 4i 则进一步展示了 diverse epithelial populations 中合理的 signature activation。

---

## 图 5：SIGnature 可以用于疾病图谱搜索并产生可实验验证的生物学发现

![SIGnature Figure 5](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/SIGnature-fig5.png)

Fig. 5 证明了 SIGnature 可以从大规模疾病图谱中发现 MS1-like 疾病相关状态。作者再以 Kawasaki disease (KD) 为重点，证明这种状态在表达程序和体外实验中都成立，并找到了一个体外诱导 MS1-like 表型的实验条件，即 KD serum。

不过，作者没有找到直接抑制 MS1-like 状态的方法，而是说明 IVIG 后 MS1-like cells 的下降不能简单归因于 IgG 的直接抑制作用。这个结果让文章从“方法有效”进一步推进到“可以支持疾病状态检索和实验假说生成”。

---

## 结果组织方式总结

这篇文章的结果组织很清晰：Fig. 1 先用概念图和几个代表性结果建立方法直觉；Fig. 2 系统比较 foundation model、归因方法和评价指标，确认技术路线；Fig. 3 展开证明 attribution 的生物学意义、抗技术噪声和跨研究泛化；Fig. 4 把单基因 attribution 推广到 gene signature activation；Fig. 5 再进一步落到疾病图谱搜索和实验验证。

这种组织方式的关键不是单纯展示“指标更高”，而是不断扩大方法的使用半径：从单细胞内的基因重要性，到跨研究 gene program，再到 signature scoring，最后到疾病图谱检索和实验假说。

---

### 🔗 相关资源

* **论文原文**: [DOI: 10.1038/s41587-026-03112-5](https://doi.org/10.1038/s41587-026-03112-5)
