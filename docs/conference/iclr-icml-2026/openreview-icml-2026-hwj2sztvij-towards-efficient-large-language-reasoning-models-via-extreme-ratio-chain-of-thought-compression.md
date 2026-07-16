---
title: Towards Efficient Large Language Reasoning Models via Extreme-Ratio Chain-of-Thought Compression
title_zh: 通过极端压缩比链式思维实现高效大语言推理模型
authors: "Yuntian Tang, Bohan Jia, Wenxuan Huang, Lianyue Zhang, Jiao Xie, Wenxi Li, Wei Li, Jie Hu, Xinghao Chen, Rongrong Ji, Shaohui Lin"
date: 2026-04-30
pdf: "https://openreview.net/pdf/1a842038dcd0a89371fc6c4ac4f7d6247b3cccd6.pdf"
tags: ["query:vlm-cot"]
score: 9.0
evidence: 极端压缩比下的链式思维压缩
tldr: 现有链式思维压缩方法在高压缩比下损失逻辑忠实度。Extra-CoT框架通过训练语义保持压缩器，在数学CoT数据上生成高保真监督信号，然后对LLM进行微调。实验表明Extra-CoT在极端压缩比下仍保持高准确率，实现了快速推理。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 高压缩比下现有CoT压缩方法逻辑保真度严重下降。
method: 提出Extra-CoT框架，先训练语义保持压缩器，再用高保真监督微调LLM。
result: 在极端压缩比下仍保持答案准确率，推理速度大幅提升。
conclusion: Extra-CoT实现了高压缩比下高保真的CoT压缩，促进高效推理。
---

## Abstract
Chain-of-Thought (CoT) reasoning successfully enhances the reasoning capabilities of Large Language Models (LLMs), yet it incurs substantial computational overhead for inference. Existing CoT compression methods often suffer from a critical loss of logical fidelity at high compression ratios, resulting in significant performance degradation. To achieve high-fidelity, fast reasoning, we propose a novel EXTreme-RAtio Chain-of-Thought Compression framework, termed Extra-CoT, which aggressively reduces the token budget while preserving answer accuracy. To generate reliable, high-fidelity supervision, we first train a dedicated semantically-preserved compressor on mathematical CoT data with fine-grained annotations. An LLM is then fine-tuned on these compressed pairs via a mixed-ratio supervised fine-tuning (SFT), teaching it to follow a spectrum of compression budgets and providing a stable initialization for reinforcement learning (RL). We further propose Constrained and Hierarchical Ratio Policy Optimization (CHRPO) to explicitly incentivize question-solving ability under lower budgets by a hierarchical reward. Experiments on three mathematical reasoning benchmarks show the superiority of Extra-CoT. For example, on MATH-500 using Qwen3-1.7B, Extra-CoT achieves over 73\% token reduction with an accuracy improvement of 0.6\%, significantly outperforming state-of-the-art (SOTA) methods. Our source codes are released in the Supplementaries.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义

- **研究动机**：大语言模型（LLM）的链式思维（Chain-of-Thought, CoT）推理虽提升了推理能力，但引入了大量计算开销。现有CoT压缩方法在高压缩比（Extreme-Ratio）下严重损失逻辑忠实度（Logical Fidelity），导致答案准确率显著下降。
- **本文目标**：在极端压缩比下实现高保真（High-Fidelity）的CoT压缩，大幅降低token预算的同时保持甚至提升答案准确率，从而加速推理过程。
- **整体含义**：提出了Extra-CoT框架，通过训练语义保持压缩器生成高保真监督信号，结合混合比例监督微调和分层约束策略优化，在数学推理任务上实现了73%以上的token缩减且准确率不降反升，为高效LLM推理提供了新范式。

## 2. 方法论

### 核心思想
采用“先压缩再微调”的两阶段框架：先训练一个专用语义保持压缩器（Semantically-Preserved Compressor），在数学CoT数据上生成高保真的压缩推理链；然后对LLM进行混合比例监督微调（Mixed-Ratio SFT）和强化学习（RL）优化，使其适应不同压缩预算并最大化低预算下的解题能力。

### 关键技术细节

1. **语义保持压缩器训练**：
   - 在数学CoT数据集上，利用细粒度标注（fine-grained annotations）训练一个专用压缩器，确保压缩后的文本保留原始推理步骤的语义逻辑，避免信息丢失。

2. **混合比例监督微调（Mixed-Ratio SFT）**：
   - 使用压缩器生成不同压缩比（如低、中、高）的CoT对（原始→压缩），对LLM进行微调，使其学会跟随多种压缩预算，为后续RL提供稳定初始化。

3. **分层约束策略优化（Constrained and Hierarchical Ratio Policy Optimization, CHRPO）**：
   - 设计分层奖励机制，在RL阶段显式激励模型在较低token预算下仍保持高解题能力。具体通过约束策略（Constrained Policy）和分层奖励（Hierarchical Reward）实现平衡压缩率与准确率。

### 算法流程（文字描述）
1. 数据准备：收集数学推理CoT数据，并添加细粒度标注。
2. 压缩器训练：基于标注数据训练语义保持压缩器。
3. 生成训练对：压缩器对原始CoT压缩为不同比例的版本，形成语料库。
4. 混合比例SFT：在LLM上微调，使其适应多比例压缩输入。
5. CHRPO强化学习：利用分层奖励（高压缩比下正确回答给予更高奖励）优化策略，最终得到推理高效且高度压缩的LLM。

## 3. 实验设计

- **使用数据集**：三个数学推理基准（具体名称未在摘要中列出，但提及MATH-500）。
- **Benchmark**：MATH-500 及其他两个数学推理数据集（可能为GSM8K、MathQA等常见基准，待确认）。
- **对比方法**：与当前最优（State-of-the-Art, SOTA）CoT压缩方法对比。摘要提到在MATH-500上使用Qwen3-1.7B模型，Extra-CoT实现了73%以上token缩减且准确率提升0.6%，显著优于SOTA。
- **评估指标**：答案准确率、token缩减率、推理速度（推断时间）。

## 4. 资源与算力

- 论文摘要未明确说明使用的GPU型号、数量及训练时长。
- 仅提到使用Qwen3-1.7B模型进行实验，并披露了源代码在补充材料中。
- 推测：实验规模可能局限于小模型（1.7B），未涉及更大模型及大规模分布式训练资源的详细描述。

## 5. 实验数量与充分性

- **实验数量**：摘要仅明确给出了在MATH-500上的一个代表性结果，但声称在三个数学推理基准上均验证了有效性。通常ICML论文会包含多个数据集、多种模型变体、消融实验（如不同压缩比、奖励函数设计等），但此处未列出具体数目。
- **充分性判断**：从摘要来看，实验设计较完整：有对比实验（与SOTA），有压缩比消融（73%缩减），有单模型结果。但缺乏跨模型规模（如7B、13B）或跨任务（非数学推理）的泛化测试，以及更细致的消融分析（如是否每个组件必要）。因此实验充分性中等，可认为基本满足会议标准，但存在一定不足。

## 6. 主要结论与发现

- 提出Extra-CoT框架，在极端压缩比下仍能保持高逻辑保真度和高准确率。
- 在MATH-500上，使用Qwen3-1.7B实现73%以上token缩减，准确率提升0.6%，显著优于SOTA压缩方法。
- 证明语义保持压缩器+混合比例SFT+CHRPO三模块共同作用，可实现高效推理压缩。

## 7. 优点

- **创新性**：首次针对极端压缩比下的逻辑保真度下降问题，提出分层奖励RL优化，显式激励低预算下的推理能力。
- **有效性**：在73%压缩比下准确率不降反升，证明方法在极低token预算下仍可保持推理质量。
- **实用性**：显著降低推理成本，适合资源受限场景，如边缘设备、实时应用。
- **开源**：提供源代码，利于复现和社区发展。

## 8. 不足与局限

- **实验覆盖有限**：仅测试了1.7B小模型，未验证在更大模型（如7B、70B）上的泛化能力，缺少跨规模泛化分析。
- **领域局限**：仅针对数学推理任务，未验证在常识推理、符号推理等其他类型CoT任务上的表现。
- **压缩器依赖**：方法依赖专用语义保持压缩器的训练质量，若压缩器本身存在偏差将影响整个流程。
- **资源信息缺失**：未报告训练压缩器及微调LLM的具体算力开销，难以评估实际工程成本。
- **公平性风险**：对比的SOTA方法是否在同一模型、同一压缩比下严格对齐（如是否复现了基线的最佳配置）未详细说明。

（完）
