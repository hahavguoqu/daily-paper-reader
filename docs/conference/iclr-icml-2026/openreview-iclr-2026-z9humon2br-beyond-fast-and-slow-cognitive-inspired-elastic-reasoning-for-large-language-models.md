---
title: "Beyond Fast and Slow: Cognitive-Inspired Elastic Reasoning for Large Language Models"
title_zh: 超越快慢：面向大语言模型的认知启发弹性推理
authors: "Jinwu Hu, Dongjin Yang, LangYu Bian, Zhiquan Wen, Yufeng Wang, Yaofo Chen, Bin Xiao, Yuanqing Li, Mingkui Tan"
date: 2025-09-02
pdf: "https://openreview.net/pdf?id=z9HUMon2Br"
tags: ["query:vlm-cot"]
score: 7.0
evidence: 提出受认知启发的弹性推理框架，处理类似思维链的慢思考
tldr: 现有大语言模型推理策略难以兼顾效率与准确性，尤其对难度不同的查询。该文提出认知启发弹性推理（CogER），通过评估查询复杂度并将其分配到预定义级别的处理策略，模拟人类分层推理。实验表明该方法在查询多样性下有效平衡了推理速度与准确性。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 现有LLM推理策略在查询难度变化时难以平衡效率与准确性。
method: 提出CogER框架，先评估查询复杂度，再将其分配给不同级别的处理策略。
result: 在多种查询难度下实现了推理效率与准确性的良好平衡。
conclusion: 认知启发的弹性推理为LLM自适应推理提供了新思路。
---

## Abstract
Large language models (LLMs) have demonstrated impressive performance across various language tasks. However, existing LLM reasoning strategies mainly rely on the LLM itself with fast or slow mode (like o1 thinking) and thus struggle to balance reasoning efficiency and accuracy across queries of varying difficulties. In this paper, we propose Cognitive-Inspired Elastic Reasoning (CogER), a framework inspired by human hierarchical reasoning that dynamically selects the most suitable reasoning strategy for each query. Specifically, CogER first assesses the complexity of incoming queries and assigns them to one of several predefined levels, each corresponding to a tailored processing strategy, thereby addressing the challenge of unobservable query difficulty. To achieve automatic strategy selection, we model the process as a Markov Decision Process and train a CogER-Agent using reinforcement learning. The agent is guided by a reward function that balances solution quality and computational cost, ensuring resource-efficient reasoning. Moreover, for queries requiring external tools, we introduce Cognitive Tool-Assisted Reasoning, which enables the LLM to autonomously invoke external tools within its chain-of-thought. Extensive experiments demonstrate that CogER outperforms state‑of‑the‑art Test‑Time scaling methods, achieving at least a 13% relative improvement in average exact match on In‑Domain tasks and an 8% relative gain on Out‑of‑Domain tasks.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

现有的大语言模型（LLM）推理策略主要依赖模型自身以快或慢模式（如o1式思考）进行推理，但面对难度各异的查询时，难以同时兼顾推理效率与准确性。即：简单查询可能因过度推理而浪费算力，复杂查询又可能因策略过于简单而降低正确率。受人类分层推理的启发，作者提出**认知启发弹性推理（Cognitive-Inspired Elastic Reasoning，CogER）**框架，旨在根据查询实际难度动态选择最合适的推理策略，从而实现资源高效且准确的推理。

## 2. 方法论

### 核心思想
CogER 模拟人类认知中“先评估难度，再分配处理层级”的机制，将查询分为多个预定义难度级别，每个级别对应一种定制的处理策略（例如从直接生成到长链思维、工具调用等）。通过自动化策略选择，实现推理速度与质量的弹性平衡。

### 关键技术细节
- **复杂度评估**：首先对输入查询的复杂度进行评估，将其分配到多个预定义级别之一（级别数量及对应的处理策略由系统预先设定）。这一步骤解决了查询难度难以直接观测的问题。
- **策略选择建模（MDP）**：将自动策略选择过程建模为**马尔可夫决策过程（MDP）**。
- **训练智能体（CogER-Agent）**：使用**强化学习**训练一个智能体来执行策略选择。奖励函数同时考虑**解的质量**（如准确率）与**计算成本**（如推理步数或调用次数），引导智能体在效率和准确性间做出权衡。
- **工具辅助推理（Cognitive Tool-Assisted Reasoning）**：对于需要外部工具的查询，框架允许LLM在思维链（Chain-of-Thought）过程中自主调用外部工具，进一步提升复杂任务的处理能力。

### 算法流程（文字说明）
1. 输入查询 → 复杂度评估模块 → 输出难度级别标签。
2. 根据级别标签，智能体选择对应预配置的推理策略（例如：简单查询直接生成答案；中等查询使用标准思维链；困难查询使用带工具调用的长链推理）。
3. 执行选定策略，得到最终答案。
4. 强化学习训练阶段：智能体在大量查询上学习，奖励函数 = 准确性加分 - 计算成本惩罚。

## 3. 实验设计

### 数据集 / 场景
- 论文进行了域内（In-Domain）和域外（Out-of-Domain）任务的评估。具体数据集名称未在摘要中列出，但提及了“In-Domain tasks”和“Out-of-Domain tasks”。

### 基准（Benchmark）
- 与当前最先进的**测试时扩展方法（Test‑Time scaling methods）**进行对比。

### 对比方法
- 未列出具体方法名称，但声称CogER显著优于SOTA测试时扩展方法。

### 实验结果
- 域内任务：平均精确匹配（Exact Match）相对提升至少 **13%**。
- 域外任务：平均精确匹配相对提升至少 **8%**。

## 4. 资源与算力

摘要及元数据中**未明确说明**使用的GPU型号、数量、训练时长等算力信息。需要指出这一点：论文未提供具体的计算资源细节。

## 5. 实验数量与充分性

从摘要看，实验覆盖了域内和域外两类任务，并与其他SOTA方法进行了对比，相对提升显著。但是：
- 未提及具体的数据集数量、消融实验组数、超参数敏感性分析等细节。
- 由于是ICLR 2026投稿，全文可能包含更丰富的实验（如不同模型大小、不同复杂度级别数量等），但当前摘要信息有限。
- **充分性评估**：仅从摘要难以判断实验的全面性，但至少报告了两个不同场景下的结果，并声称优于SOTA，表明实验设计具有一定客观性。但缺少对框架关键组件（如复杂度评估模块、奖励函数形式）的消融分析，可能不够充分。

## 6. 主要结论与发现

- CogER框架能够在多样化难度的查询上有效平衡推理速度与准确性。
- 相比现有的测试时扩展方法，CogER在域内任务上获得13%以上的相对提升，在域外任务上获得8%以上的相对提升，说明其具备良好的泛化能力。
- 认知启发的弹性推理为LLM自适应推理提供了新思路，通过分层策略选择实现了资源高效利用。

## 7. 优点

- **创新性**：将人类认知分层机制引入LLM推理，提出弹性策略选择框架，不同于传统的固定快慢模式。
- **实用性**：通过奖励函数显式权衡效率与准确性，降低不必要的计算开销。
- **泛化性**：在域外任务上依然保持显著提升，表明模型学到的是通用难度评估与策略选择能力。
- **可扩展性**：支持外部工具调用，可处理更复杂的查询。

## 8. 不足与局限

- **实验细节缺失**：未提供数据集列表、具体超参数、消融实验等，难以完全复现和验证。
- **对复杂度评估模块的依赖**：如果评估错误，可能导致策略选择不当，影响最终效果。摘要未讨论该模块的鲁棒性。
- **计算资源未报告**：无法评估框架本身的训练和推理成本。
- **未讨论多任务、多语言等场景**：仅提及域内/域外，可能覆盖不够广泛。
- **仅对比测试时扩展方法**：未与传统基线（如标准CoT、直接生成等）详细比较，可能存在其他更优的方法未被纳入对比。
- **应用限制**：需要预定义难度级别及对应的处理策略，框架的灵活性受限于预设配置。

（完）
