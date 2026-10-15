---
title: "ICLR 2026 | Conference Paper  AUTOFIGURE: Generating and Refining Publication-Ready Scientific Illustrations"
collection: paper_reading
category: autoresearch
permalink: /paper_reading/autofigure
---

> **碎碎念**：AUTOFIGURE 框架通过“代码中转”的方式，成功将 Gemini 等大模型的推理重心从模糊的“像素生成”（png/jpg）转向了精确的“逻辑规划”（SVG/HTML）。

---

## 1. Motivation (研究动机)
* **传播瓶颈**：高质量科研图示对传播复杂科研概念至关重要，但其手动创建仍是瓶颈。
* **创作高门槛**：制作一张出版级图片通常需要研究员花费数天时间，且要求创作者同时具备深厚的领域知识和专业设计技能。
* **AI 科学家进化需求**：随着自主 AI 科学家的兴起，赋予系统将复杂的机器发现转化为人类易懂视觉语言的能力，已成为自动化科研流的必然选择。

---

## 2. Key Issues (核心问题)
论文指出，现有的自动化绘图技术在处理长文本科研任务（平均 >10k tokens）时存在以下痛点：
* **“搬运”而非“创造”**：诸如 PosterAgent 等现有系统主要侧重于重排和提取已有内容，而非通过理解文本生成原创视觉逻辑。
* **结构与美学的两难困境**：
    * **代码生成类（如 TikZ 或 SVG）**：虽能精准满足几何与结构约束，但在美学流畅度和排版可读性上表现不佳。
    * **端到端文生图模型（如 GPT-Image）**：图像质量高，但在长文本下难以保持结构忠实度，易产生逻辑幻觉且文本模糊。
* **长文本推理压力**：从超长文档中提取方法论并自主规划 2D 布局对 AI 的长程理解能力构成了挑战。

---

## 3. Methods (技术路线)
AUTOFIGURE 采用了**推理渲染 (Reasoned Rendering)** 范式，将生成过程解构为三个阶段：

![AUTOFIGURE 框架 Pipeline](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/autofigure.png)

### Stage I: 概念提炼与布局生成
* **语义解析**：将非结构化长文本转化为机器可读的**符号化布局**（SVG/HTML），明确几何形状和拓扑结构。
* **内容蒸馏**：提取实体（Nodes）和逻辑关系（Edges），生成核心方法论摘要。

### Stage II: 批判与精修 (Critique-and-Refine)
* **多智能体博弈**：模拟“AI 设计师”与“AI 评论员”的迭代对话，通过循环搜索找到全局最优布局。
* **全局优化**：评论员评估布局的对齐、平衡和重叠避免，通过反馈引导设计师改进。

### Stage III: 渲染策略与文本后处理
* **审美渲染**：将验证后的符号化蓝图转化为高保真图像，确保视觉风格专业且统一。
* **“擦除与纠正”策略**：移除背景模糊文本，利用 OCR 结合原始标签交叉验证，最后以矢量形式填入纠正后的文本，确保 **100% 文本准确度**。

---

## 4. Evaluation (实验评估)
* **FigureBench 基准测试**：构建了首个针对长文本科研绘图的基准，包含 3,300 对高质量科学文本-图示对。
* **多元 Baseline 对比**：对比了端到端 T2I (GPT-Image)、文本转代码 (Gemini-HTML/SVG) 以及多智能体框架 (Diagram Agent)。
* **评估协议**：
    * **VLM-as-a-judge**：基于 InternVL 3.5 进行维度打分，验证显示 VLM 评分与人类偏好高度一致。
    * **领域专家研究**：邀请 10 位论文一作针对其发表工作的生成结果进行评估。

> ### 🚀 核心结果
> * **出版级标准**：**66.7%** 的人类专家愿意将生成结果直接用于正式出版的论文中。
> * **性能 SOTA**：在教科书类别中胜率高达 **97.5%**，显著解决了逻辑忠实度与美感的权衡问题。

---

### 🔗 相关资源
* **代码**: [GitHub - ResearAI/AutoFigure](https://github.com/ResearAI/AutoFigure)
* **论文原文**: [ICLR 2026](https://openreview.net/forum?id=5N3z9JQJKq)
