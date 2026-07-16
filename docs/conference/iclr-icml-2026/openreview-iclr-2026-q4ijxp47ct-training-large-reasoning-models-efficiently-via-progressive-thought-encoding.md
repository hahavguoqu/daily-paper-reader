---
title: Training Large Reasoning Models Efficiently via Progressive Thought Encoding
title_zh: 通过渐进式思维编码高效训练大型推理模型
authors: "Zeliang Zhang, Xiaodong Liu, Hao Cheng, Hao Sun, Chenliang Xu, Jianfeng Gao"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=q4iJxp47CT"
tags: ["query:vlm-cot"]
score: 9.0
evidence: 通过渐进式思维编码直接提升大型推理模型的训练效率
tldr: 大型推理模型在强化学习训练中需要长展开，导致显存和速度瓶颈。该文提出渐进式思维编码，通过将中间推理逐步压缩为紧凑表示，使模型在固定尺寸缓存下有效推理。方法消除了全缓存反向传播的需要，训练显存恒定且不损害推理性能。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: LRM的RL训练需要长展开，内存和时间开销巨大。
method: 提出渐进式思维编码，在固定尺寸缓存下将逐步推理编码为紧凑表示。
result: 训练显存恒定，速度提升，推理性能不受影响。
conclusion: 为LRM的高效训练提供了参数高效的微调方法。
---

## Abstract
Large reasoning models (LRMs) excel on complex problems but face a critical barrier to efficiency: reinforcement learning (RL) training requires long rollouts for outcome-based rewards, where autoregressive decoding dominates time and memory usage. While sliding-window cache strategies can bound memory, they disrupt long-context reasoning and degrade performance. We introduce Progressive Thought Encoding, a parameter-efficient fine-tuning method that enables LRMs to reason effectively under fixed-size caches. By progressively encoding intermediate reasoning into compact representations, our approach eliminates the need to backpropagate through full-cache rollouts, thereby reducing training-time memory usage, while maintaining constant memory during inference. Experiments on three models, including Qwen2.5-3B-Instruct, Qwen2.5-7B-Instruct, and DeepSeek-R1-Distill-Llama-8B, across six widely used challenging mathematical benchmarks show consistent gains: our method achieves +19.3\% improvement over LoRA and +29.9\% over the baseline on average, with up to +23.4 absolute gains on AIME2024/2025 under tight cache budgets. These results demonstrate that Progressive Thought Encoding not only improves reasoning accuracy but also makes RL training of LRMs substantially more efficient and scalable under real-world memory constraints.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机与背景）

- **研究动机**：大型推理模型（LRMs）在复杂数学推理任务上表现优异，但其强化学习（RL）训练面临严重效率瓶颈。RL训练需要执行长序列的“展开”（rollout）以获取基于结果的奖励，而自回归解码过程导致时间和显存消耗极大。
- **核心问题**：现有的滑动窗口缓存策略虽然可以限制显存使用，但会破坏长上下文推理，导致性能下降。如何在不牺牲推理准确性的前提下，实现LRMs在固定尺寸缓存下的高效训练和推理，是本文要解决的关键问题。
- **整体含义**：提出一种参数高效的微调方法——渐进式思维编码，使LRMs能够在固定尺寸缓存下有效推理，显著降低训练显存开销，同时保持甚至提升推理性能，为LRMs的规模化训练提供了可行路径。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：将长推理过程中的中间思维逐步编码为紧凑表示，使得模型在固定尺寸缓存下进行推理时，不需要维护完整的缓存，也不需要在整个缓存上进行反向传播。
- **关键技术细节**：
  - **渐进式思维编码（Progressive Thought Encoding）**：在每一步推理中，将当前生成的中间推理步骤（思维）压缩成一个固定长度的紧凑向量，并累积存储。这样，模型只需依赖这些紧凑的编码表示，而无需保留原始的长序列缓存。
  - **消除全缓存反向传播**：传统方法需要对完整的缓存展开进行反向传播，而本方法仅对固定尺寸的编码表示进行梯度计算，训练显存保持恒定。
  - **推理阶段**：同样使用固定尺寸的缓存（紧凑编码）进行自回归生成，显存消耗不随序列长度增长。
  - **参数高效微调**：方法基于LoRA等参数高效技术，只调整少量参数，适用于预训练模型的微调。

## 3. 实验设计

- **数据集 / 场景**：6个广泛使用的具有挑战性的数学基准数据集，包括AIME2024、AIME2025等（具体列表未全部列出，但明确提到了AIME2024/2025）。
- **基准模型**：Qwen2.5-3B-Instruct、Qwen2.5-7B-Instruct、DeepSeek-R1-Distill-Llama-8B。
- **对比方法**：
  - 基线（baseline）：标准全缓存RL训练。
  - LoRA：低秩适配方法。
  - 本文的方法（Progressive Thought Encoding）。
- **评价指标**：推理准确率（accuracy）和训练效率（显存使用、速度）。

## 4. 资源与算力

- 论文中**未明确说明**具体的GPU型号、数量、训练时长等算力信息。仅提到训练显存恒定、速度提升等定性结果，但未给出详细的硬件配置和训练时间数据。

## 5. 实验数量与充分性

- **实验数量**：
  - 在3个不同规模（3B, 7B, 8B）的模型上进行实验。
  - 覆盖6个数学基准数据集。
  - 包含与LoRA和基线方法的对比，以及AIME2024/2025的绝对增益分析。
- **充分性评价**：
  - 模型规模跨度合理（3B到8B），数据集覆盖常见挑战性数学题，对比方法包括当前主流参数高效方法LoRA，实验设计较为全面。
  - 但缺乏更大规模模型（如70B+）的实验验证，也未涉及其他类型推理任务（如代码、科学推理），泛化性有待确认。
  - 消融实验方面：论文未明确提及是否进行了消融实验（如不同编码维度、缓存大小的影响），可能是一个缺失。

## 6. 主要结论与发现

- 本文方法在平均性能上比LoRA提升**+19.3%**，比基线提升**+29.9%**。
- 在AIME2024/2025上，严格缓存预算下获得**+23.4绝对增益**。
- 训练显存保持恒定，不随序列长度增长，大幅降低训练成本。
- 推理性能不仅没有下降，反而有所提升，说明渐进式思维编码有助于模型提炼关键信息。
- 该方法为LRMs的高效RL训练提供了可行且可扩展的解决方案。

## 7. 优点：方法或实验设计亮点

- **方法创新**：首次将渐进式编码思想应用于LRM的RL训练，解决长序列展开的显存瓶颈，属于参数高效微调（PEFT）范畴。
- **固定显存开销**：训练和推理阶段显存恒定，使得模型可以部署在有限显存设备上，具备实际应用价值。
- **性能提升而非牺牲**：在提高效率的同时，推理准确率显著提升，体现了编码带来的信息压缩与聚焦优势。
- **多模型验证**：在两个主流模型系列（Qwen2.5、DeepSeek-R1蒸馏版）上均取得一致增益，说明方法具有跨模型泛化能力。

## 8. 不足与局限

- **硬件信息缺失**：未报告具体GPU型号、数量、训练时间等，难以复现和评估算力需求。
- **消融实验不足**：未详细分析渐进式编码的各个组件（如编码长度、压缩率、是否替换缓存等）的影响，对方法各部分的贡献缺乏透明证明。
- **实验规模有限**：最大模型仅为8B，未测试更大模型（如13B、70B）上的扩展性，也未验证在非数学推理任务（如代码生成、法律推理）上的效果。
- **潜在偏差风险**：仅使用数学基准，可能过度强调数学推理能力；未说明数据集中是否存在标签泄露或简单模式。
- **应用限制**：方法依赖参数高效微调，可能不完全适用于从头训练超大规模LRM；在需要全模型参数更新的场景下可能不具优势。

（完）
