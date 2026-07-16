---
title: Causal Dependency-Aware Unsupervised Routing for Large Reasoning Models
title_zh: 面向大型推理模型的因果依赖感知无监督路由
authors: "Jiacheng Liu, Hao Liu, Xiaofeng Hou, Wei Xue, Yike Guo"
date: 2026-04-30
pdf: "https://openreview.net/pdf/0c3f9a46eeae4176e0365367b4a0513c029564be.pdf"
tags: ["query:vlm-cot"]
score: 7.0
evidence: 针对大型推理模型的因果思考结构提出无监督路由方法
tldr: 大型推理模型（LRMs）的输出具有因果思考-回答结构，给无监督路由带来新挑战。该文提出因果依赖感知的无监督路由方法，利用LRM的内部结构进行路由，无需人工标注。方法通过捕捉思考过程的因果依赖关系，在分布变化下保持鲁棒性。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有监督路由需要大量标注数据且泛化性差，LRM的因果结构更增加了难度。
method: 提出因果依赖感知的无监督路由方法，利用LRM的思考-回答因果结构进行决策。
result: 在无需标注情况下实现了鲁棒的路由性能，适应分布变化。
conclusion: 该方法为LRM生态系统中的高效路由提供了可行的无监督方案。
---

## Abstract
As Large Language Model (LLM) ecosystems grow, routing queries to the most suitable model in a diverse pool has become a critical strategy for building efficient and high-performing AI systems.  
A common approach is to train a supervised router; however, this requires vast, expensive human-annotated preference data and creates models that are notoriously brittle, failing to generalize when faced with inevitable distribution shifts in user queries. Consequently, developing robust, unsupervised routing methods that adapt without retraining is a crucial research frontier. 
This challenge is severely amplified by Large Reasoning Models (LRMs), which introduce a dual problem for any label-free method: their outputs have a causal thinking $\to$ answer structure that must be modeled, and a structural imbalance where long reasoning text can dominate the final answer signal. We introduce ReasoningRouter, a novel framework that resolves these issues with a length-balanced embedding strategy and a probabilistic model capturing the thinking-to-answer dependency. 
The proposed Causal Triangulation Property enables the label-free estimation of component qualities and their causal link. 
Beyond competitive routing accuracy, ReasoningRouter offers unprecedented insights into model behavior, enabling separate quality assessment of reasoning and answer components while maintaining computational efficiency.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

随着大语言模型（LLM）生态的扩张，将查询路由到最合适的模型成为构建高效、高性能AI系统的关键策略。现有方法多采用监督式路由器，但需要大量昂贵的人工标注偏好数据，并且模型在面对用户查询不可避免的分布偏移时泛化能力差、容易失效。因此，开发无需重新训练的、鲁棒的无监督路由方法是重要的研究前沿。  
大型推理模型（LRM）进一步放大了这一挑战：其输出具有因果的“思考→回答”结构（需建模）以及结构不平衡（长推理文本可能掩盖最终回答信号），给无标签方法带来双重难题。

## 2. 方法论：核心思想、关键技术细节

**论文提出的方法：ReasoningRouter**  
- **核心思想**：利用LRM内部生成的因果思考过程进行无监督路由，无需人工标注。通过长度平衡的嵌入策略和概率模型捕捉“思考→回答”的因果依赖关系。  
- **关键技术细节**：  
  - 提出**因果三角属性（Causal Triangulation Property）**，使得能够无标签地估计组件质量及其因果联系。  
  - 采用**长度平衡嵌入策略**解决长推理文本对回答信号的掩盖问题。  
  - 构建**概率模型**显式建模思考到回答的依赖性。  
- **算法流程（文字说明）**：  
  1. 对每个LRM的输出，分离思考部分和回答部分，分别提取嵌入表示。  
  2. 使用长度平衡策略消除推理长度带来的偏差。  
  3. 通过因果三角属性无监督地估计每个模型在思考质量和回答质量上的得分。  
  4. 结合思考-回答依赖关系进行路由决策，选择最合适的模型。  
（论文未给出具体公式，以上根据摘要推理。）

## 3. 实验设计

**数据集/场景**：摘要及元数据中未明确列出具体数据集名称或评价场景。  
**Benchmark**：未提及。  
**对比方法**：未提及具体对比基线，但上下文暗示与监督路由方法以及可能其他无监督方法对比。  
**说明**：由于提供的材料仅为元数据摘要，实验细节缺失，无法判断具体设置。

## 4. 资源与算力

文献中未说明使用的GPU型号、数量、训练时长等任何算力信息。仅能从“ICML-2026”会议判断为大规模实验，但具体资源未知。需指出**未明确说明**。

## 5. 实验数量与充分性

仅从摘要可知论文进行了**竞争性的路由准确性**评估，并提供了对模型行为的洞察（如推理和回答组件质量的独立评估）。但实验组数（如不同分布偏移、消融实验、跨数据集测试）均未提及。  
**评价**：鉴于只有摘要，无法评估实验的充分性、客观性和公平性。论文声称在无需标注下实现鲁棒性能，但缺乏公开的详细实验证明。

## 6. 主要结论与发现

- ReasoningRouter在无需任何标注数据的情况下，实现了具有竞争力的路由准确性。  
- 方法能够适应分布变化，保持鲁棒性。  
- 提供了对模型行为的深刻洞察，能够独立评估推理组件和回答组件的质量。  
- 同时保持了计算效率。

## 7. 优点

- **无监督性**：摆脱了对昂贵人工标注的依赖，降低了部署成本。  
- **因果结构建模**：专门针对LRM的思考-回答结构设计，解决了现有方法无法处理的结构不平衡问题。  
- **鲁棒性**：在分布偏移下无需重新训练即可泛化。  
- **可解释性**：能分离评估推理质量和回答质量，有助于模型理解。

## 8. 不足与局限

- **实验信息缺失**：提供的材料（仅元数据摘要）无法验证实验的完整性与可靠性。例如：未报告数据集规模、对比基线、统计显著性等。  
- **偏差风险**：方法仅基于LRM的因果结构，若模型输出不符合该结构（如短思考或无思考）可能失效。  
- **应用限制**：仅适用于具有明显思考-回答结构的LRM，对其他类型模型（如传统LLM）可能不适用或需调整。  
- **计算开销**：虽提及计算效率，但未与监督方法进行具体量化比较。  
- **缺乏代码或复现细节**：无法独立评估方法的可复现性。

（完）
