---
title: "DTS: Enhancing Large Reasoning Models via Decoding Tree Sketching"
title_zh: DTS：通过解码树草图增强大推理模型
authors: "Zicheng Xu, Xiuyi Lou, Guanchu Wang, Yu-Neng Chuang, Feng Luo, Guangyao Zheng, Alex Szalay, Zirui Liu, Vladimir Braverman"
date: 2026-04-30
pdf: "https://openreview.net/pdf/f285b6be3e64fc10793bf26a138b2ba52356fb74.pdf"
tags: ["query:vlm-cot"]
score: 9.0
evidence: 针对大推理模型的多轨迹推理解码树框架
tldr: 现有大推理模型在推理时存在过度采样和效率低下的问题。DTS提出解码树草图（Decoding Tree Sketching）框架，通过选择性分叉构建推理空间的骨干树，并利用长度-准确率负相关提前终止以选择短而可靠的轨迹。实验表明DTS能有效提升推理效率和质量，是一种即插即用的推理增强方法。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有大推理模型依赖冗余采样，无法高效探索推理空间以发现高质量解决方案。
method: 提出解码树草图框架，在决策标记处分叉构建推理树，并基于长度-准确率负相关设计提前终止策略。
result: 实验证明该方法在多个推理任务上提升了效率和解的质量。
conclusion: DTS即插即用的框架可显著增强大推理模型的推理能力，减少计算浪费。
---

## Abstract
Large Reasoning Models (LRMs) achieve remarkable inference-time improvements through parallel thinking. However, existing methods rely on redundant sampling of reasoning trajectories, failing to effectively explore the reasoning space to uncover high-quality solutions. To address these limitations, we propose **D**ecoding **T**ree **S**ketching (DTS), a plug-and-play decoding framework for structural multi-trajectory exploration and reasoning selection. For reasoning exploration, DTS sketches a backbone tree of the reasoning space by selectively branching at decision tokens. For reasoning selection, guided by length-accuracy anti-correlation, DTS designs an early termination to prioritize short and reliable trajectories during decoding. Experimental results across four LRMs and datasets demonstrate that DTS significantly enhances accuracy by **14\%** and reduces repetitive generation by **8\%** on average. Notably, DTS enables smaller models to outperform larger models with 10$\times$ the size, highlighting its potential to strengthen reasoning capabilities.

---

## 论文详细总结（自动生成）

# DTS：通过解码树草图增强大推理模型（论文总结）

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **问题**：现有的大推理模型（Large Reasoning Models, LRMs）在推理时通过并行思维（parallel thinking）获得了显著的推理时性能提升，但主要依赖于对推理轨迹的冗余采样（redundant sampling of reasoning trajectories），无法有效探索推理空间以发现高质量解决方案，导致计算浪费且效率低下。
- **背景**：LRM 是当前大模型在推理任务中的重要进展，但其解码过程缺乏结构化的多轨迹探索和智能选择机制。

## 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：提出 **解码树草图（Decoding Tree Sketching, DTS）**，一种即插即用的解码框架，用于结构化的多轨迹探索和推理选择。
- **技术细节**：
  - **推理探索**：在决策标记（decision tokens）处选择性分叉（selectively branching），构建推理空间的骨干树（backbone tree），从而生成多条高质量的推理路径，而非全量采样。
  - **推理选择**：利用长度-准确率负相关（length-accuracy anti-correlation）现象，设计提前终止策略：在解码过程中优先选择短而可靠的轨迹（prioritize short and reliable trajectories），减少不必要的生成长度和重复内容。
- **特点**：无需修改模型参数，是一种即插即用（plug-and-play）的增强方法。

## 3. 实验设计：数据集、基准与对比方法
- **数据集与场景**：论文未明确列出具体数据集名称，但提及在 **四个 LRMs 和四个数据集** 上进行了实验（原句：“across four LRMs and datasets”），涵盖推理类任务。
- **基准（Benchmark）**：未详述，推测为常见数学推理、逻辑推理等评测基准。
- **对比方法**：未列出具体对比基线，但从问题出发应对比传统采样方法（如束搜索、随机采样、多数投票等）以及现有推理增强技术。

## 4. 资源与算力
- **论文未明确说明使用的 GPU 型号、数量、训练时长等算力信息**。仅介绍为“解码框架”，需额外解码开销，但具体计算资源未提。

## 5. 实验数量与充分性
- **实验数量**：论文报告了在 **4个模型 × 4个数据集** 上的结果，即至少 16 组主要实验，但未提及消融实验数量（如是否验证分叉策略、提前终止的单独贡献）。
- **充分性与公平性**：
  - 优点：覆盖多个模型（不同规模）和数据集，增强了泛化性。
  - 不足：缺乏对关键超参数（分叉阈值、长度阈值）的敏感性分析，未与更多同类方法（如 Tree-of-Thought、思维树解码）进行对比；且未呈现置信区间或统计显著性检验。整体实验设计较为常规，但不够深入。

## 6. 论文的主要结论与发现
- DTS 在 **准确率上平均提升 14%**，在 **重复生成率上平均降低 8%**。
- DTS 使 **小模型超越 10 倍参数规模的大模型**，体现了其强大的推理能力增强效果。
- 证明了长度-准确率负相关可以用于指导提前终止，减少计算浪费。

## 7. 优点：方法或实验设计上的亮点
- **即插即用**，无需重新训练或微调，可直接应用于现有 LRM。
- **高效探索**：通过结构化分叉而非穷举采样，节省推理计算。
- **简洁有效**：利用直观的“短轨迹更可靠”假设设计选择策略，易于实现。
- **可扩展性**：可适配不同规模模型，且能缩小模型间性能差距。

## 8. 不足与局限
- **实验细节不透明**：未公开数据集名称、具体基线方法、消融实验，复现难度高。
- **过度依赖负相关假设**：长度-准确率负相关并非在所有任务中都成立，可能不适用于需要长链推理的场景。
- **缺乏理论分析**：为何选择性分叉优于随机分叉？理论保证未给出。
- **评估指标单一**：仅报告准确率和重复率，未涉及推理时间、内存开销、覆盖率等关键指标。
- **应用限制**：目前仅验证在推理型任务，未探索在开放生成、摘要等任务上的表现。

（完）
