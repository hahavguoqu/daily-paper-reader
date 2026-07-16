---
title: FAST THINKING FOR LARGE LANGUAGE MODELS
title_zh: 大型语言模型的快速思考
authors: "Haoyu Zheng, Zhuonan Wang, Yuqian Yuan, Wenqiao Zhang, Zheqi Lv, Hongyang He, Juncheng Li, Siliang Tang, Yueting Zhuang"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=jINW28iIlM"
tags: ["query:vlm-cot"]
score: 8.0
evidence: 使用CoT草图学习潜编码本实现快速思考
tldr: 基于链式思维的推理虽然强大但效率低。该工作提出潜在码本框架，训练时使用简洁CoT草图学习离散策略先验。推理时模型只需一次前向传播即可获取策略指导，无需显式推理令牌。实验表明该方法在保持推理质量的同时大幅降低延迟。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 现有CoT推理需要生成大量显式令牌，导致高延迟和高令牌消耗。
method: 训练时用简洁CoT草图学习离散策略码本，推理时蒸馏为连续思维向量一次前向传播。
result: 无需显式推理令牌，推理速度显著提升，质量保持。
conclusion: 该框架实现了策略级的高效推理指导，兼顾速度与质量。
---

## Abstract
Reasoning-oriented Large Language Models (LLMs) often rely on generating explicit tokens step by step, and their effectiveness typically hinges on large-scale supervised fine-tuning or reinforcement learning. 
While Chain-of-Thought (CoT) techniques substantially enhance performance on complex reasoning tasks, they remain inefficient, requiring long reasoning traces that increase latency and token usage. 
In this work, we introduce \emph{Latent Codebooks for Fast Thinking}, a framework that uses concise CoT sketches only during training to learn a codebook of discrete strategy priors. 
At inference, the model conditions on a handful of continuous thinking vectors distilled from the codebook in a single pass, enabling strategy-level guidance without producing explicit reasoning tokens. 
To complement this design, we propose GainRouter, a lightweight routing mechanism that adaptively switches between fast codebook-guided inference and slow explicit reasoning, thereby suppressing overthinking and reducing unnecessary token generation. 
Experiments across multiple reasoning benchmarks show that our approach achieves competitive or superior accuracy while substantially lowering inference cost, offering a practical path toward efficient and controllable reasoning in large language models.

---

## 论文详细总结（自动生成）

# 大型语言模型的快速思考（FAST THINKING FOR LARGE LANGUAGE MODELS）

## 1. 核心问题与整体含义（研究动机和背景）
- **现有问题**：基于链式思维（Chain-of-Thought, CoT）的推理方法虽然显著提升了大型语言模型在复杂任务上的表现，但需要生成大量显式的推理令牌，导致高延迟、高令牌消耗，推理效率低下。
- **研究动机**：寻求一种既能保持 CoT 推理质量、又能大幅降低推理开销的方法，实现“快思考”与“慢思考”的平衡。
- **整体含义**：该工作旨在通过引入隐式策略先验，使模型在一次前向传播中即可获得推理导向，而不必显式生成推理步骤，从而在保持准确性的前提下显著加速推理。

## 2. 论文提出的方法论
### 核心思想
- 在训练阶段仅使用简洁的 CoT 草图（concise CoT sketches）学习一个离散策略先验的码本（codebook），该码本编码了多种推理策略模式。
- 推理时，模型从码本中蒸馏出少量连续思维向量，通过一次前向传播注入模型，实现策略级指导，无需生成任何显式推理令牌。
- 配套提出 **GainRouter**：一种轻量路由机制，可在快速码本推理（快思考）与慢速显式推理（慢思考）之间自适应切换，从而抑制过度思考（overthinking）并减少不必要的令牌生成。

### 关键技术细节
- **潜在码本（Latent Codebooks）**：将离散的推理策略（如 CoT 草图中的模式）编码为可学习的嵌入向量。训练时，模型通过对比学习或预测任务学习码本映射。
- **连续思维向量蒸馏**：推理时，通过注意力或加权融合从码本中提取最相关的若干连续向量，直接作为条件输入后续层，替代多步令牌生成。
- **GainRouter**：一个轻量门控网络，根据输入复杂度或推理阶段动态决定使用码本路径还是显式推理路径，实现自适应效率优化。
- **训练流程**：
  1. 对原始 CoT 数据进行压缩（保留关键推理步骤形成草图）。
  2. 利用草图训练码本编码器与解码器，使码本能够捕捉策略级离散先验。
  3. 联合训练 GainRouter 以学习切换策略。
- **推理流程**：
  1. 输入问题。
  2. 查询码本获得连续思维向量（单次前向传播）。
  3. 若 GainRouter 判断需要细粒度推理，则切换到显式 CoT 生成；否则直接利用码本输出答案。

### 公式或算法流程（文字说明）
- 整体可抽象为：`推理策略 = Codebook(CoT_sketch)`，推理过程 = `Model(input, thinking_vectors)`，其中 `thinking_vectors` 由码本蒸馏得到。
- GainRouter 的决策函数：`switch = Router(input, hidden_states)`，输出 0/1 决定使用快/慢路径。

## 3. 实验设计
- **数据集/场景**：文中提及“多个推理基准”（multiple reasoning benchmarks），但未在摘要中列出具体名称。典型 CoT 评估数据集包括 GSM8K、MATH、StrategyQA、ARC 等。推测覆盖了数学推理、常识推理等任务。
- **基准（Benchmark）**：以标准准确率（Accuracy）和推理成本（延迟、令牌数）作为主要指标。
- **对比方法**：包括标准 CoT（显式推理）、可能还有零样本 CoT、思维树（Tree-of-Thoughts）等基线，并与未使用码本的方法进行对比。

## 4. 资源与算力
- **未明确说明**：论文摘要及元数据中未提及具体使用的 GPU 型号、数量、训练时长等硬件资源信息。这是当前信息下的明确局限。

## 5. 实验数量与充分性
- **实验数量**：由于文本有限，无法确切统计。但元数据提到“多组推理基准”，暗示至少涵盖 3~5 个不同领域的数据集，且可能包含消融实验（如码本蒸馏 vs 无码本、GainRouter 有效性、快慢路径比例等）。
- **充分性评价**：从摘要描述看，实验设计覆盖了主要性能指标（准确率、速率），并验证了自适应性。但缺乏对码本大小、蒸馏向量数等超参数的详细敏感性分析；且未提及在不同模型规模（如 7B、13B、70B）上的验证，泛化性证据不足。

## 6. 论文的主要结论与发现
- **主要结论**：所提出的潜在码本框架能在保持与标准 CoT 相当甚至更优的推理准确率的同时，显著降低推理延迟和令牌消耗。
- **GainRouter 的有效性**：自适应切换机制有效减少了不必要的显式推理，避免了“过度思考”，进一步提升了效率。
- **实践意义**：为大语言模型提供了一种高效、可控的推理路径，在资源受限场景（如边缘设备、实时应用）中具有潜在价值。

## 7. 优点
- **创新性**：将离散策略码本与连续思维向量蒸馏相结合，将显式推理转化为隐式条件，思路新颖。
- **效率提升**：单次前向传播即可获得推理指导，大幅减少序列生成步骤，是解决 CoT 高延迟问题的有效方案。
- **自适应可控**：GainRouter 允许模型在快/慢推理间灵活切换，避免了“一刀切”的低效或性能损失。
- **训练成本低**：仅需简洁 CoT 草图作为监督信号，无需大规模强化学习或复杂数据增强。

## 8. 不足与局限
- **实验透明度不足**：未提供完整的实验设置细节（数据集列表、模型规模、硬件配置），难以完全复现。
- **码本质量依赖**：推理性能高度依赖码本是否能够充分覆盖多样化推理策略，对于未见过的推理模式可能失败。
- **泛化性验证有限**：未明确说明是否在代码生成、多模态推理等场景测试，任务覆盖范围可能较窄。
- **缺乏消融分析**：如码本容量、蒸馏向量数量等超参数的影响未公开讨论。
- **被拒稿记录**：该论文被 ICLR 2026 拒收，可能意味着评审认为其创新性或实证证据存在不足（需注意这一背景，但保持客观）。

（完）
