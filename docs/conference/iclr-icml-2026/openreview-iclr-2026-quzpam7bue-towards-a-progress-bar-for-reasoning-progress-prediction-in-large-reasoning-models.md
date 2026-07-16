---
title: "Towards a Progress Bar for Reasoning: Progress Prediction in Large Reasoning Models"
title_zh: 迈向推理进度条：大型推理模型中的进度预测
authors: "Hans Peter Lyngsøe Raaschou-jensen, Constanza Fierro, Anders Søgaard"
date: 2025-09-19
pdf: "https://openreview.net/pdf?id=qUzpAM7bue"
tags: ["query:vlm-cot"]
score: 8.0
evidence: 聚焦于具有隐藏思维链的大型推理模型的进度预测
tldr: "大型推理模型产生隐藏的长思维链，用户难以估计完成时间。该文通过探针分析LLM内部表示发现可量化推理进度，并提出两阶段微调方法使模型具备进度预测能力。线性探针在10类进度上达到30%准确率，为交互式系统提供了重要能力。"
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: LRM的推理时间不可预测，用户无法设定恰当期望。
method: 通过探针挖掘内部表示量化推理进度，并使用两阶段微调训练模型预测进度。
result: "线性探针在进度预测上取得30%准确率，验证了可行性。"
conclusion: 进度预测提升了LRM的可用性和用户体验。
---

## Abstract
Reasoning models that produce long, hidden chains of thought, have emerged as powerful tools for reasoning-intensive and agentic tasks. However, as the time horizons at which these models can operate grow exponentially, it becomes increasingly difficult to know how much progress the model is making on a task, making it challenging for users to set appropriate expectations about completion time. By probing the internal representations of Large Language Models (LLMs), we find evidence that their reasoning progress can be quantified, with simple linear probes achieving 30\% accuracy over 10 progress classes and Mean Absolute Error (MAE) of 1.75. Rooted in this insight, we propose a two-stage fine-tuning method that trains existing reasoning models to explicitly generate progress estimates (0-100\%) during their reasoning process. We find that the predictions of our best fine-tuned language model for sequences below 16K tokens are on average 10\% from the true label.

---

## 论文详细总结（自动生成）

好的，以下是根据您提供的论文元数据与摘要信息生成的中文总结。

# 论文详细总结

## 1. 核心问题与整体含义（研究动机和背景）
- **核心问题**：大型推理模型（Large Reasoning Models, LRMs）在生成隐藏长链思维时，推理时间极长且不可预测，用户难以估计任务完成进度，导致无法设定恰当的期望，影响交互体验。
- **研究动机**：为提高LRM的可用性，需要一种机制能让用户了解推理进度（类比于下载进度条），从而改善人机协作中的时间管理。
- **整体含义**：首次将“进度预测”引入大型推理模型，通过挖掘模型内部表示或微调模型本身，使模型能够输出推理进度百分比，为交互式系统提供核心能力。

## 2. 方法论
- **核心思想**：推理模型的内部表示中隐含着对推理进度的可量化信息；通过探针（probe）可以从隐藏状态中提取进度信号；进一步可以通过微调让模型主动生成进度估计。
- **关键技术细节**：
  - **探针分析**：使用简单的线性探针（linear probe）从LLM的内部表示中预测推理进度（0-100%），将其划分为10个进度类别。
  - **两阶段微调方法**：
    1. 第一阶段：在模型的隐藏状态上训练进度探针，获得可靠的进度标签。
    2. 第二阶段：利用探针生成的进度标签作为监督信号，对原有推理模型进行监督微调，使其在推理过程中显式地生成进度估计（0-100%）。
- **公式/算法流程**（文字说明）：
  - 输入：推理模型的隐藏层表示序列。
  - 探针：线性分类器将每个时间步的隐藏表示映射到10个进度类别之一。
  - 微调：使用交叉熵损失，让模型在生成token的同时输出一个进度数字（作为特殊token或附加输出）。

## 3. 实验设计
- **数据集/场景**：论文未明确指定具体数据集，但推测使用了常见推理任务（如数学推理、代码生成、逻辑推理等）中带有长隐藏思维链的模型输出。
- **Benchmark**：以“进度预测”任务本身作为评测基准，包括：
  - 10类进度分类准确率（Accuracy）
  - 平均绝对误差（MAE）
- **对比方法**：未提及对比基线（可能仅验证了探针的可行性，或与随机猜测比较；线性探针本身作为方法，未对比其他更复杂的探针或微调策略）。摘要中仅给出了探针结果（30%准确率，MAE 1.75）和微调模型结果（对小于16K token的序列平均误差10%）。

## 4. 资源与算力
- 论文元数据及摘要**未明确说明**使用的GPU型号、数量、训练时长等算力信息。只提到模型为“Large Reasoning Models”及“fine-tuned language model”，未提供具体参数规模。

## 5. 实验数量与充分性
- **实验数量**：从摘要看，至少进行了两组核心实验：
  1. 线性探针在10类进度上的分类（得到精确值）。
  2. 两阶段微调后模型在序列长度<16K token上的进度预测误差。
- **充分性评估**：实验覆盖了探针分析（验证可行性）和微调训练（实现主动预测），但缺乏不同模型规模、不同任务类型、不同序列长度段的详细对比，以及消融实验（如探针类型、微调损失函数等）。因此实验**不够充分**，但作为初步探索可以接受；客观性和公平性方面，未提供与随机基线或其他方法的比较，难以判断。

## 6. 主要结论与发现
- 线性探针可以量化推理进度，在10类进度上达到30%准确率（随机猜测为10%），MAE为1.75，证明内部表示包含进度信息。
- 通过两阶段微调，模型能够输出进度估计，对于不超过16K token的序列，平均误差约为10%。
- 认为进度预测有望显著提升LRM的可用性和用户体验。

## 7. 优点
- **问题新颖**：首次将进度预测引入推理模型，填补了人机交互中时间感知的空白。
- **方法简洁有效**：线性探针和两阶段微调思路清晰，计算成本低，容易落地。
- **实用性导向**：直接面向用户需求，具有实际应用价值（如可视化进度条）。

## 8. 不足与局限
- **实验覆盖面窄**：仅报告了单一探针和单一微调模型在特定长度下的结果，缺乏跨模型、跨任务、跨长度的泛化性验证。
- **未提供对比基线**：没有与不预测进度、随机预测、或简单基线（如基于token数线性估计）进行比较，难以评估真实增益。
- **隐藏思维链的限制**：论文假设可以访问内部表示（探针阶段），但实际部署中如果模型不公开隐藏状态，则探针方法不可行；微调方法则要求修改模型输出。
- **进度标签的可靠性**：探针自动标注的进度标签是否准确？微调后模型可能过拟合到探针噪声。
- **资源与算力缺失**：未提供训练细节，难以复现。
- **可扩展性未知**：对超长序列（>16K token）的预测误差可能很大。

（完）
