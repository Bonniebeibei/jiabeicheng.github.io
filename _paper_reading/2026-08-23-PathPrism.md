---
title: "Cancer Cell | Article  Spatial biomarker discovery via interpretable semantic learning in histopathology"
collection: paper_reading
category: ai4bio
permalink: /paper_reading/pathprism
---

这篇文章要解决的问题是：如何把 H&E 中与疾病相关、但难以由人直接发现和描述的空间模式，转化为**可定义、可计算、可验证的 spatial biomarkers**。

作者提出 PathPrism：先让深度学习从 H&E 中发现有用的空间模式，再把这些黑盒模式翻译成可解释的 spatial biomarkers，最后用这些 biomarkers 做临床预测、生成机制假说，并进行虚拟扰动。

全文的主线可以概括为：

**H&E → semantic tissue map → 黑盒模式发现 → white-box spatial biomarkers → 临床预测与机制假说 → virtual perturbation**

---

## Motivation：为什么需要高通量的 spatial biomarker discovery

传统 biomarker 挖掘依赖人工观察和专家解释，因此难以扩展：

> “Yet biomarker discovery remains **labor-intensive** and **reliant on expert interpretation**” ([Liang et al., 2026, p. 1607](zotero://select/library/items/9IRAHE54)) ([pdf](zotero://open-pdf/library/items/UP8G6EG2?page=3))

Spatial biomarker 的发现尤其困难，因为组织异质性、复杂的空间组织，以及微小的视觉模式经常超过人类可以解释的范围：

> “Spatial biomarkers, defined by multiscale and multimodal patterns within the tumor microenvironment (TME), hold particular promise but are difficult to discover because **tissue heterogeneity, complex spatial organization**, and **subtle visual patterns often exceed the capacity of human interpretation.**” ([Liang et al., 2026, p. 1607](zotero://select/library/items/9IRAHE54)) ([pdf](zotero://open-pdf/library/items/UP8G6EG2?page=3))

因此，作者希望把 biomarker 挖掘也变成一种高通量形式，使其同时具备高通量、可量化和可解释三个特征：

> “This mismatch between abundant histopathological data and limited human throughput motivates **high-throughput, quantitative, and interpretable approaches to spatial biomarker discovery.**” ([Liang et al., 2026, p. 1607](zotero://select/library/items/9IRAHE54)) ([pdf](zotero://open-pdf/library/items/UP8G6EG2?page=3))

---

## Fig. 1：定义问题，并提出 PathPrism 的完整框架

![PathPrism Figure 1](https://raw.githubusercontent.com/Bunnybeibei/jiabeicheng.github.io/master/images/PathPrism-fig1.png)

传统 pathology foundation model 可以预测，但内部通常是 latent embedding，很难进一步回答：**到底是哪一种可解释的组织空间结构与疾病有关？**

**Fig. 1a** 先建立一个人和模型都能理解的中间表示空间：把 CRC WSI 分成 8 类组织。这等于提出了一种新的 WSI 表示。

**Fig. 1b** 大量丢弃原始 H&E morphology，只保留 8 种组织的空间布局，依然可以预测生存。这验证了全文最重要的前提：**宏观组织空间结构本身包含临床信息。**

**Fig. 1c** 根据 MacroNet 解释得到的空间模式线索，进一步人为定义并计算一组 white-box spatial biomarkers，也就是基于 semantic tissue map 构造的 628 个可解释空间描述参数。

我的理解是，这一步有些类似 SPARK 产生大量特征：当候选特征足够丰富时，预测效果随之提高。不同的是，这里的特征及其公式由作者自行设计，再通过枚举组织类型和组织对进行扩展。

**Fig. 1d** 使用简单、透明的模型证明这些 biomarkers 确实能够完成临床任务，对应 **Find** 过程。

**Fig. 1e** 因为 biomarker 已经变成语义化语言，所以 LLM 可以参与机制推理，对应 **Explain** 过程。

例如，不再给 LLM 一个没有直接语义的 embedding dimension 437，而是告诉它：

> lymphocyte–mucin connectivity fragmentation 与 chemotherapy response 有关。

这时 LLM 才能围绕 pathology concept 提出机制假说。这里产生的是待验证的生物学解释，而不是因果机制本身。

**Fig. 1f** 进一步把 biomarker 从“观察变量”变成可以 perturb 的变量。判别模型没有改变，改变的是输入：观察 spatial biomarker 如何变化、patient 在 biomarker atlas 中如何移动，以及预测 outcome 如何随之变化。

**Fig. 1g** 说明后续实验来自多个大型 CRC cohort，交代数据集构成。

总的来说，Fig. 1 用总览图串起了全文流程，并提前对应后续各个 Results。

---

## Fig. 2：证明 semantic tissue architecture 是有效的 disease representation

![PathPrism Figure 2](https://raw.githubusercontent.com/Bunnybeibei/jiabeicheng.github.io/master/images/PathPrism-fig2.png)

Fig. 2 首先证明 PathPrism 的两个基础组件确实能够工作，即 **semantic tissue architecture 是一个足够强的 disease representation**。这一结果为后续只基于组织语义图而非完整 H&E morphology 进行模式解释和 biomarker 计算提供基础。

---

## Fig. 3：发现应该量化什么样的空间模式

![PathPrism Figure 3](https://raw.githubusercontent.com/Bunnybeibei/jiabeicheng.github.io/master/images/PathPrism-fig3.png)

Fig. 3 是 Fig. 4 设计 feature family 的 empirical rationale / motivation。它的逻辑是：

**MacroNet 能预测 → 它到底依据了什么？ → 用多种解释方法寻找 candidate spatial biomarkers**

**Fig. 3a** 定义最简单的两类解释量，也就是区分某一种组织“很多”和某一种组织“对模型很重要”。

**Fig. 3b** 比较 high-risk 与 low-risk，说明 MacroNet 不是单纯在学习“某种组织有多少”，也在学习“这些组织如何排列”。

**Fig. 3c** 进一步分析 tissue–tissue relationship。作者计算 pairwise correlation 和 co-occurrence，例如发现低风险病例中 **LYM–STR** 经常共现。

**Fig. 3d** 说明作者认为 attribution 仍然不够，因此加入 counterfactual perturbation。

**Fig. 3e** 展示 STR 被逐步 suppression / enhancement 的例子。

**Fig. 3f** 系统改变不同 tissue，观察预测 risk 如何变化。

**Fig. 3g** 进一步按 high-risk / low-risk group 比较 perturbation response。

**Fig. 3h** 让病理学家重新检查具体的 spatial architecture。

**Fig. 3i** 把“看起来更乱”量化成 spatial entropy：

**model interpretation 中观察到 disorganization → 转化为一个可以计算的 feature：spatial entropy**

**Fig. 3j–k** 将 model attribution 再映射回 H&E morphology。

因此，Fig. 3 的作用并不是直接宣布 biomarker，而是从模型解释中提炼候选空间模式，为 Fig. 4 的数学定义和 cohort-level validation 提供依据。

---

## Fig. 4：将候选模式数学化并扩展为 628 个 white-box features

![PathPrism Figure 4](https://raw.githubusercontent.com/Bunnybeibei/jiabeicheng.github.io/master/images/PathPrism-fig4.png)

Fig. 4 把 Fig. 3 中发现的模式正式数学化、批量展开，并进行验证：

- Fig. 3 看到**“多少”重要** → Fig. 4 设计 **spatial fraction**；
- Fig. 3 看到**“乱不乱 / fragmentation”重要** → Fig. 4 设计 **spatial entropy / graph topology**；
- Fig. 3 看到 **LYM–STR、STR–TUM 等组织关系重要** → Fig. 4 设计 **multi-tissue graph features**；
- Fig. 3 看到 **boundary / connectivity / invasion pattern** → Fig. 4 使用 **distance、edge density、connected components、centrality、modularity** 等图指标量化。

完整流程是：

**MacroNet interpretation**

↓

提出 candidate hypothesis，例如 TUM spatial entropy 或 LYM–STR interaction

↓

根据 semantic map 写成 quantitative descriptors

↓

在 cohort 中进行 survival validation

作者先人工设计一套 feature family，再将其穷举应用于所有 tissue 和 tissue pair，组合扩展成 628 个 candidate spatial biomarkers。

需要注意的是，**628 个 features 并不是 open-ended discovery**。它们仍然受限于作者预先定义的 8 个 tissue types 和 graph-statistics vocabulary。论文也承认，PathPrism 受 predefined semantic tissue representations 限制。

真正进入 biomarker 验证阶段的前提是：

> **必须有明确数学定义，并且能够在 cohort level 独立计算和检验。**

---

## Fig. 5：检验 spatial biomarker spectrum 的通用疾病表征能力

![PathPrism Figure 5](https://raw.githubusercontent.com/Bunnybeibei/jiabeicheng.github.io/master/images/PathPrism-fig5.png)

Fig. 5 证明这个 spatial biomarker spectrum 不只是一种 prognosis representation，也是更通用的 disease representation。也就是说，这组可解释参数的价值不应局限于单一生存预测任务，而在于提供一个能够支持多种临床问题的共同空间描述空间。

---

## Fig. 6：从统计学发现走向 biological hypothesis

![PathPrism Figure 6](https://raw.githubusercontent.com/Bunnybeibei/jiabeicheng.github.io/master/images/PathPrism-fig6.png)

Fig. 6 将统计学发现进一步转化为 biological hypothesis。因为输入不再是抽象 latent dimension，而是具有病理语义的 tissue relationship、connectivity 或 fragmentation，LLM 才能够围绕这些概念组织已有知识并提出可检验的解释。

这一步完成的是**机制假说生成**，其价值在于帮助研究者确定后续验证方向；仅凭 biomarker 与结局的关联及 LLM 推理，还不能把这些假说视为已证实的机制。

---

## Fig. 7：从被动关联进入主动探索——VirtualWSI

![PathPrism Figure 7](https://raw.githubusercontent.com/Bunnybeibei/jiabeicheng.github.io/master/images/PathPrism-fig7.png)

Fig. 7 通过 VirtualWSI 从“被动关联”进入“主动探索”。

PathPrism 确实把高维 WSI 压缩成 628 个可解释参数，但 VirtualWSI 的主要扰动方式并不是直接修改这 628 个参数中的某一个数字，而是：

**修改 semantic tissue map → 重新计算 628 个参数 → 保持判别模型不变 → 观察预测结果如何变化**

因此，作者可以追踪 spatial biomarker 如何改变、patient 在 biomarker atlas 中如何移动，以及预测 outcome 如何随输入变化。这类 virtual perturbation 提供的是模型内的反事实探索，不等同于对真实组织进行生物学干预。

---

## 学习点

这篇文章最重要的设计，是在人类病理概念与深度学习 latent representation 之间建立了一层 **semantic tissue map**。它既保留了足以预测疾病结局的宏观空间结构，又允许研究者把模型线索翻译成具有明确公式的 white-box features。

Fig. 3 与 Fig. 4 的衔接尤其值得学习：先通过模型解释回答“哪些空间现象值得量化”，再把这些现象写成可以批量计算和 cohort-level 检验的指标。这样，模型 attribution 不再是终点，而成为 biomarker 设计的 empirical rationale。

同时也要看到方法边界：其 discovery space 由 8 种预定义组织类别和人工选择的图统计词汇表共同限定。LLM 生成的解释和 VirtualWSI 的反事实变化分别属于假说生成与模型内扰动，仍需要独立实验或病理证据才能支持机制和因果结论。

---

## 相关资源

- **论文原文**：[DOI: 10.1016/j.ccell.2026.05.014](https://doi.org/10.1016/j.ccell.2026.05.014)
- **图片来源**：Liang et al., *Cancer Cell* (2026), Copyright © 2026 Elsevier Inc.
