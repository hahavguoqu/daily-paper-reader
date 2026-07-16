---
title: Efficient Inference with Large Reasoning Models
title_zh: 大规模推理模型的高效推理
authors: "Junda Wang, Zhichao Yang, Dongxu Zhang, Robert E. Tillman, Sanjit Singh Batra"
date: 2025-09-20
pdf: "https://openreview.net/pdf?id=WOIf5MGJXB"
tags: ["query:vlm-cot"]
score: 8.0
evidence: 链式思维推理效率
tldr: 大型推理模型在生成长链式思维时存在冗余推理，导致计算浪费。ESTAR方法通过轨迹分类器识别安全停止点，并结合监督微调和强化学习，教模型自生成停止信号。在四个推理数据集上，ESTAR将推理长度减少约3.7倍，同时保持准确率。该工作为高效推理提供了新方向。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 大型推理模型生成长链式思维虽然准确但存在大量冗余，浪费计算资源。
method: 提出ESTAR框架，结合轨迹分类器、SFT和RL，教模型自生成停止信号以提前终止推理。
result: 在四个推理数据集上，推理长度减少约3.7倍，准确率无损。
conclusion: ESTAR有效降低了推理成本，为高效推理提供了可扩展方法。
---

## Abstract
Large reasoning models (LRMs) achieve state-of-the-art performance by generating long chains-of-thought, but often waste computation on redundant reasoning after the correct answer has already been reached. We introduce Early-Stopping for Token-Aware Reasoning (ESTAR) that detects and reduces such reasoning redundancy to improve efficiency without sacrificing accuracy. Our method combines (i) a trajectory-based classifier that identifies when reasoning can be safely stopped, (ii) supervised fine-tuning to teach LRMs to propose self-generated <stop> signals, and (iii) <stop>-aware reinforcement learning that truncates rollouts at self-generated stop points with compute-aware rewards. Experiments on four reasoning datasets show that ESTAR reduces reasoning length by about x3.7 (from 4799 to 1290) while preserving accuracy (74.9% vs. 74.2%), with strong cross-domain generalization. These results highlight early stopping as a simple yet powerful mechanism for improving reasoning efficiency in LRMs

---

## 论文详细总结（自动生成）

# 大规模推理模型的高效推理：ESTAR 方法详解

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：大型推理模型（LRMs）通过生成长链式思维（Chain-of-Thought）来提升推理准确性，但在得到正确答案后常继续产生冗余推理，导致大量计算资源浪费。作者提出需要检测并消除这种冗余，以在不影响准确率的前提下降低推理成本。
- **整体含义**：该工作针对 LRMs 推理效率瓶颈，首次系统性地引入“早期停止”机制，通过让模型自生成停止信号来截断冗余推理，为高效推理提供了一种可扩展、低损耗的新范式。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：Early-Stopping for Token-Aware Reasoning (ESTAR)，即训练模型在推理过程中自主判断何时可以安全停止，并输出 `<stop>` 信号，从而提前终止生成。
- **关键技术细节**：
  - **(i) 轨迹级分类器**：训练一个二分类器，对推理过程中的每一个中间状态（轨迹）判断是否已达到可停止点。该分类器基于模型隐藏状态或生成概率，识别冗余推理区间。
  - **(ii) 监督微调 (SFT)**：使用轨迹分类器标注的停止点数据，通过监督学习教会 LRMs 在适当的时刻生成 `<stop>` 特殊标记。
  - **(iii) `<stop>` 感知强化学习**：在强化学习阶段，将 rollouts 在模型自生成的 `<stop>` 位置截断，并设计计算量感知的奖励函数（compute-aware rewards），鼓励模型在保证正确率的前提下尽早停止。
- **整体流程**：先收集推理轨迹 → 训练分类器 → 用分类器标注停止点构造 SFT 数据 → 微调模型 → 再通过 RL 优化停止策略。

## 3. 实验设计：数据集、基准、对比方法

- **数据集**：在四个推理数据集上评估，涵盖不同领域（如数学、逻辑等）。原文未列出具体数据集名称，但元数据暗示为常见推理 benchmark（可能包括 GSM8K、MATH 等）。
- **基准（Benchmark）**：主要对比原始 LRMs（未停止）与 ESTAR 方法的推理长度和准确率。
- **对比方法**：论文未提及与其他高效推理方法的直接比较，主要与 baseline（无停止）进行消融和跨域泛化测试。

## 4. 资源与算力

- **未明确说明**：文中未提供 GPU 型号、数量、训练时长等具体算力信息。仅可推断需要训练一个分类器并执行 SFT + RL，算力需求应与标准 LRM 微调相当。

## 5. 实验数量与充分性

- **实验组数**：主要报告了四个数据集上的主实验结果（推理长度和准确率），以及跨领域泛化实验。元数据提到“在四个推理数据集上”，可能还包含消融实验（如去掉分类器、去掉 RL 等），但原文未详细列出。
- **充分性评估**：实验覆盖了多个数据集，且验证了跨域泛化，说明方法具有普遍性。但缺乏与现有高效推理方法（如知识蒸馏、模型剪枝、推理步数压缩）的对比，实验公平性存在一定局限。消融实验（分类器、SFT、RL 各组件贡献）是否充分未知。

## 6. 论文的主要结论与发现

- ESTAR 方法在四个推理数据集上将推理长度平均减少约 **3.7 倍**（从 4799 个 token 降至 1290 个 token）。
- 准确率几乎无损失：74.9%（ESTAR） vs. 74.2%（原始模型）。
- 跨域泛化能力强，表明早期停止机制可适用于不同推理任务。
- 早期停止是一种简单而强大的提升 LRMs 推理效率的机制。

## 7. 优点：方法或实验设计上的亮点

- **方法简洁有效**：将“早期停止”概念系统化，并结合分类器、SFT、RL 三种技术实现端到端训练，无需改动模型架构。
- **计算效率提升显著**：3.7 倍长度缩减且准确率无损，对于实际部署具有直接经济价值。
- **跨域泛化验证**：实验不仅局限单一领域，增强了方法的鲁棒性和实用性。
- **奖励函数设计**：计算量感知奖励能更合理地平衡速度与准确性。

## 8. 不足与局限

- **实验细节不充分**：原文（基于提供的摘要和元数据）未详细列出数据集名称、数值波动、消融实验结果等，难以全面评估方法优劣。
- **缺少与现有方法的对比**：未与蒸馏、量化、早期退出（early exit）等常见高效推理方法进行公平比较，削弱了结论的说服力。
- **资源信息缺失**：未报告训练计算成本，无法判断方法本身是否过重。
- **可能的应用限制**：早期停止依赖 `<stop>` 信号，若推理任务本身需要完整中间步骤验证（如数学推导过程），过早停止可能丢失可解释性。
- **分类器准确性依赖**：轨迹分类器的性能直接影响停止点质量，若分类器误判则可能降低准确率，文中未深入分析分类器鲁棒性。

（完）
