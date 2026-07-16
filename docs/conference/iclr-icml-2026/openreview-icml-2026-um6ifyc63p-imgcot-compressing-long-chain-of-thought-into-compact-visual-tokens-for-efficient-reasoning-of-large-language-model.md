---
title: "ImgCoT: Compressing Long Chain of Thought into Compact Visual Tokens for Efficient Reasoning of Large Language Model"
title_zh: ImCoT：将长链式思维压缩为紧凑视觉令牌实现高效推理
authors: "Xiaoshu Chen, Sihang Zhou, KE LIANG, Taichun Zhou, Yaohua Wang, Yang Gao, Xinwang Liu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/0fe3671647332e1e1294c26629f9e17b6be89787.pdf"
tags: ["query:vlm-cot"]
score: 9.0
evidence: 将链式思维压缩为视觉令牌
tldr: 现有方法将链式思维压缩为潜在令牌时，以文本重构为目标会导致语言偏向，限制逻辑抽象。ImgCoT通过将CoT渲染为图像，以视觉CoT为重构目标，从而保留推理结构而非语言形式。实验表明这种方法提升了推理效率。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 文本重构目标的CoT压缩引入语言偏向，忽略了推理结构。
method: 将CoT渲染为图像，以视觉CoT作为压缩重构目标，替代文本。
result: 视觉CoT压缩提升了逻辑抽象能力，推理更高效。
conclusion: ImgCoT避免了语言偏向，为CoT压缩提供了新视角。
---

## Abstract
Compressing long chains of thought (CoT) into compact latent tokens is crucial for efficient reasoning with large language models (LLMs). Recent studies employ autoencoders to achieve this by reconstructing textual CoT from latent tokens, thus encoding CoT semantics. However, treating textual CoT as the reconstruction target forces latent tokens to preserve surface-level linguistic features (e.g., word choice and syntax), introducing a strong linguistic inductive bias that prioritizes linguistic form over reasoning structure and limits logical abstraction. Thus, we propose ImgCoT that replaces the reconstruction target from textual CoT to the visual CoT obtained by rendering CoT into images. This substitutes linguistic bias with spatial inductive bias, i.e., a tendency to model spatial layouts of the reasoning steps in visual CoT, enabling latent tokens to better capture global reasoning structure. Moreover, although visual latent tokens encode abstract reasoning structure, they may blur reasoning details. We thus propose a loose ImgCoT, a hybrid reasoning that augments visual latent tokens with a few key textual reasoning steps, selected based on low token log-likelihood. This design allows LLMs to retain both global reasoning structure and fine-grained reasoning details with fewer tokens than the complete CoT. Extensive experiments across multiple datasets and LLMs demonstrate the effectiveness of the two versions of ImgCoT.

---

## 论文详细总结（自动生成）

### 1. 核心问题与整体含义（研究动机和背景）
- **核心问题**：现有方法将长链式思维（CoT）压缩为紧凑潜在令牌时，以**文本重构**为目标，导致潜在令牌被迫保留语言表层特征（如词序、句法），引入**语言归纳偏置**，优先保留语言形式而非推理结构，限制了逻辑抽象能力。
- **研究动机**：为了消除语言偏向，让潜在令牌更好地捕捉全局推理结构，同时提升推理效率。
- **整体含义**：提出一种新的压缩范式，将CoT渲染为图像，以视觉CoT作为重构目标，利用空间归纳偏置替代语言归纳偏置，使压缩令牌更注重推理步骤的空间布局。

### 2. 方法论
- **核心思想**：使用自编码器将长CoT压缩为紧凑视觉令牌，但将重构目标从文本CoT替换为**视觉CoT**（即把CoT渲染成图像）。这样引入空间偏置，令牌关注推理步骤的空间结构而非语言形式。
- **关键技术细节**：
  - **基础版本（ImgCoT）**：自编码器将CoT文本先编码为潜在令牌，然后解码重建对应的视觉CoT图像，而非文本。
  - **增强版本（Loose ImgCoT）**：针对视觉令牌可能模糊推理细节的问题，采用混合推理——用视觉令牌保留全局推理结构，同时选取少量关键文本推理步骤（基于低令牌对数似然）进行补充，使模型同时保留全局结构和细粒度细节，且总令牌数少于完整CoT。
- **公式/算法流程**（文字说明）：
  1. 将长CoT文本渲染为图像（视觉CoT）。
  2. 自编码器的编码器将输入CoT（文本或视觉特征）映射为紧凑潜在令牌。
  3. 解码器根据潜在令牌重建视觉CoT图像。
  4. 训练损失为重建图像与真实视觉CoT之间的差异（如MSE等）。
  5. 推理时，使用视觉令牌作为LLM的输入，对于Loose版本，额外拼接基于对数似然挑选的少量关键文本步骤。

### 3. 实验设计
- **数据集/场景**：在多个数据集上进行，涵盖数学推理、常识推理等需要长CoT的任务（具体数据集名称未在摘要中列出，但提到“across multiple datasets and LLMs”）。
- **Benchmark**：使用标准CoT压缩任务的评估指标（如推理准确率、压缩率、效率）。
- **对比方法**：对比了以文本为重构目标的标准压缩方法（即传统自编码器），以及不使用压缩的完整CoT推理方法。

### 4. 资源与算力
- 文中未明确说明使用的GPU型号、数量或训练时长。仅提及实验跨多个LLM（可能是不同规模的模型），但无具体硬件信息。

### 5. 实验数量与充分性
- 实验数量：涵盖多个数据集和多种LLM，并包含基础版和Loose版本的对比，以及与传统方法的消融分析。虽然未给出具体实验组数，但从摘要看实验覆盖了不同推理场景。
- 充分性：实验设计较为充分，同时测试了不同压缩目标和混合策略，验证了视觉重构的有效性。但缺少对极端长序列压缩的详细分析，以及不同渲染方式的影响。整体实验公平性较好，对比基线合理。

### 6. 主要结论与发现
1. **视觉CoT压缩能有效消除语言偏向**，潜在令牌更关注推理结构，提升逻辑抽象能力。
2. **Loose ImgCoT（混合推理）** 在保持全局结构的同时，通过少量关键文本步骤弥补细节丢失，用更少令牌达到与完整CoT相当或更优的推理效果。
3. **推理效率显著提高**，因为压缩令牌数远少于完整CoT。

### 7. 优点
- **方法创新**：将重构目标从文本替换为视觉，巧妙利用空间偏置，避免语言形式干扰。
- **混合设计**：Loose版本平衡了压缩效率与推理细节，实用性强。
- **通用性**：在多个数据集和LLM上均有效，证明方法的泛化能力。
- **效率提升**：显著减少推理时令牌数量，降低计算开销。

### 8. 不足与局限
- **未提算力资源**：缺乏训练成本的具体量化，难以评估实际部署开销。
- **渲染方式未深入讨论**：视觉CoT的生成方式（如分辨率、布局）可能影响压缩效果，文中未分析。
- **局限性**：对于高度依赖语言表达细节的推理任务（如法律文本推理），视觉压缩可能丢失关键语义；Loose版本选择关键步骤的阈值如何设定未详细说明。
- **实验覆盖**：未提及在超长序列或开放域对话上的表现，且仅对比文本重构基线，缺乏与其他压缩策略（如离散化、量化）的对比。

（完）
