---
title: "Nature | Article  Reimagining research papers as interactive and reliable AI agents"
collection: paper_reading
category: autoresearch
permalink: /paper_reading/paper2agent
---

这篇文章的故事线很清楚：它要把“论文是被动文档”的传统观念，推进为“论文是可对话、可执行、可协作的 AI agent”。四个主图构成一条由“能造出来”到“能可靠工作”再到“能产出新发现”的证据链。

---

## Fig. 1：先证明“论文可以被系统地变成 agent”

![Paper2Agent Figure 1](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/Paper2Agent-fig1.png)

Fig. 1 是整篇文章的概念与工程地基，回答的问题是：Paper2Agent 到底把什么变成了什么，以及为什么生成的 agent 值得信任？

**Fig. 1a** 证明“论文的完整研究产出”可以被包装为一个标准化、可调用的 MCP server，而不只是把 PDF 做成问答库。作者将论文、代码仓库、补充材料和数据拆为三类 agent 可用的对象：

- **MCP tools**：可执行的方法功能；
- **MCP resources**：论文、数据、代码等静态证据；
- **MCP prompts**：复杂任务的标准工作流。

将 MCP server 接入任意 LLM 后，用户便能用自然语言调用该论文的方法。这里的论点是：论文可以从知识载体变成可执行、可解释的研究对象。

**Fig. 1b** 证明这种转换不是人工手工封装，而是一个可自动化、带验证闭环的多 agent 工作流。流程是：

**定位代码库 → 环境 agent 配置依赖 → extraction agent 提炼方法并实现工具 → testing agent 反复测试、修复环境和代码 → 只保留通过验证的工具 → 部署 MCP server → 接入聊天 agent。**

关键论证在于“可靠性”不是来自 LLM 自称会做，而是来自与原始参考结果的验证：文件是否生成、数值是否在容许误差内、图是否与参考结果一致。

因此 Fig. 1 提出的核心承诺是：**自然语言降低使用门槛，而自动验证抑制代码幻觉、保障可复现性。**

---

## Fig. 2：再证明“做成 agent 后，它比直接让通用 AI 读代码更好用”

![Paper2Agent Figure 2](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/Paper2Agent-fig2.png)

Fig. 2 用 AlphaGenome 作为第一个完整案例，回答：代理化之后，是否真的覆盖原方法、答得更准、跑得更快，并能完成研究式推理？

**Fig. 2a** 证明生成的 AlphaGenome agent 不只是一个解释论文的聊天机器人，而是保留了原方法的核心可执行能力。它提供变异效应预测、跨模态可视化、训练数据链接，以及 GWAS 位点解释的工具链。用户可以提出 DNA 序列或遗传变异问题，agent 实际调用工具输出调控效应解释。这一面板支撑“方法被操作化”：方法论文的贡献被拆成了可组合的工具、资源与任务模板。

**Fig. 2b** 证明该 agent 的执行正确性优于两类基线：直接访问代码库的 Claude Code，以及 Biomni。这是在论证：经过提炼、封装和验证的工具层，比让通用 agent 临场阅读仓库更可靠。

**Fig. 2c** 证明这种可靠性并非以速度为代价。在教程任务与新任务上，Paper2Agent 的运行时间分布都更低；正文量化为相对 Claude + Repo 快 1.9–2.9 倍、相对 Biomni 快 3.1–3.8 倍。因而 Fig. 2b–c 共同构成性能论证：**Paper2Agent 同时提高准确性与效率。**

**Fig. 2d** 将论证从“答对标准题”升级为“可完成真实研究任务”。这个面板证明：agent 能将多个低层工具组成多步骤分析，并将输出提升为可读的生物学解释。同时，正文也保留了科学上的克制：其他邻近基因也有高分和 eQTL 证据，因此该结果是对既有结论的可审查再分析，而不是不可质疑的裁决。

---

## Fig. 3：进一步证明“它能稳定复现复杂工作流，而不仅是单点工具调用”

![Paper2Agent Figure 3](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/Paper2Agent-fig3.png)

Fig. 3 转向 Scanpy，解决一个更普遍的问题：大量科研分析的难点不是一个函数，而是步骤顺序、参数选择和结果解释。

**Fig. 3a** 证明 Paper2Agent 能将 Scanpy 的常用单细胞分析功能做成可调用的 MCP 工具。

**Fig. 3b** 证明复杂分析流程可被编码为 MCP prompt，而不必要求用户掌握标准流程或手动编排工具。prompt 规定了合理顺序：质控、归一化、高变基因筛选、降维、建图、聚类和注释，并让 agent 先检查数据以选择合适参数。该面板的中心主张是：**prompt 在这里不是普通提示词，而是从论文与代码中抽取出的、可复用的分析协议。**

**Fig. 3c** 是最关键的结果验证：只提供 `data.h5ad` 路径，agent 的结果与人工研究者处理同一数据的结果相匹配。因此它证明：该 agent 能在未知的公开数据集上重演研究者级别的标准工作流，不只产生一个“看起来合理”的图。

---

## Fig. 4：最后证明“论文 agent 的价值不止复现，还能跨论文协作形成新证据”

![Paper2Agent Figure 4](https://raw.githubusercontent.com/Bonniebeibei/jiabeicheng.github.io/master/images/Paper2Agent-fig4.png)

Fig. 4 是文章的终局论证，问题从“能否可靠复现已有方法”提升为：多个 paper agents 能否像跨学科研究团队那样整合独立证据，帮助科学发现？

**Fig. 4a** 证明多篇论文可以被并联为一个 AI co-scientist。三个 agent 分别来自 AlphaGenome、MPRA-coupled scCRISPRi 和 CD4+ T cell Perturb-seq 论文。它们围绕 psoriasis 相关位点 rs887314 协作：

1. AlphaGenome 预测 GPR137 是 CD4+ T 细胞中最受影响的基因；
2. 研究者在多个候选验证策略中选择“扰动特征签名相关性”；
3. agent 将 CRE 扰动后的下游表达变化，与候选基因 knockdown 的表达变化进行跨数据集比较。

所以 Fig. 4a 证明的是“多篇论文的工具与数据可被 agent 直接协调”，而非仅将多篇论文放在同一上下文窗口内总结。

**Fig. 4b** 提供这种协作得出结论的核心实验支持。GPR137 knockdown 与 rs887314 CRE 扰动的转录特征，在刺激 8 小时和 48 小时条件下显著相关（Spearman ρ 分别为 0.61、0.63；多重校正后 FDR < 0.05）；对照候选基因 BAD 则不相关。静息状态下 GPR137 的相关性也不显著。

它支持的更精确结论是：**GPR137 很可能是该位点的因果基因，且其作用具有 T 细胞激活状态依赖性。** 文章特别强调，跨 scCRISPRi 与 Perturb-seq 的“特征签名相关性”整合策略并非源论文原有分析，而是 agent 在已有证据之上提出、由人选择的验证方案。这是全文中最接近“新发现”的一环。

全文也保留了一条重要边界：**Paper2Agent 的定位是“增强研究者”，而不是取代研究判断**；特别是开放式机制解释与最终证据评价，仍需要 human-in-the-loop。

---

## Takeaway

这篇文章给我最大的启发是，一个好的 agent 工作不一定是继续做一个更“大而全”的 agent，而是找到一个新的、值得被产品化的 abstraction。

Paper2Agent 做的事情其实非常克制：它并不试图替代 Claude Code/Codex 这样的通用 agent，而是把一篇 paper 的 code、data 和 workflow 预先“编译”成经过验证的 MCP/tools，再交给通用 agent 使用。

今天看这件事似乎很自然——论文 online 时，用 Claude Code/Codex 读 repo、跑代码甚至复现论文已经越来越常见。但这篇文章在 2025 年 10 月就投稿了。它提前意识到的不是“LLM 可以帮我们跑科研代码”，而是：与其每次让一个 general agent 从头理解论文和 repo，不如把论文变成一个可靠、可执行、可组合的 agent-native object。这种对下一层 abstraction 的预见性，我觉得很值得学习。

**paper/code 很难真正被使用 → 把 paper 编译成经过验证的 MCP/tools → 为什么这比 general agent 直接读 paper + repo 更好 → reproducibility / scalability → 多个 paper agents 组合起来做新的 science。**

对我来说，最值得学习的可能不是 Paper2Agent 这个具体系统，而是这种思考方式：当基础模型和通用 agent 的能力快速增长时，新的研究机会未必来自“再做一个更强的 agent”，而可能来自提前想清楚——有哪些知识、工具或 workflow，应该被重新包装成 agent 可以可靠调用的基本单元。

---

## 相关资源

- **论文原文**：[DOI: 10.1038/s41586-026-11044-y](https://doi.org/10.1038/s41586-026-11044-y)
- **图片来源**：*Nature* (2026)，版权归原出版方所有
