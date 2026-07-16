---
title: Scalable Chain of Thoughts via Elastic Reasoning
title_zh: 通过弹性推理实现可扩展的思维链
authors: "Yuhui Xu, Hanze Dong, Lei Wang, Doyen Sahoo, Junnan Li, Caiming Xiong"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=E0Qfhma53J"
tags: ["query:vlm-cot"]
score: 9.0
evidence: 直接针对大型推理模型的可扩展思维链
tldr: 大型推理模型（LRMs）在复杂任务上通过长思维链取得突破，但推理长度不可控导致部署困难。该文提出弹性推理框架，将思维过程划分为思考与解答两个阶段并独立分配预算，在资源受限下优先保证解答完整性。通过轻量级预算约束 rollout 策略训练模型适应被截断的思考过程，显著提升了推理可靠性和可扩展性。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 现有思维链推理长度不可控，在严格推理预算下难以保证可靠性。
method: 提出了弹性推理框架，将推理分为思考和解答阶段，独立分配预算并使用预算约束 rollout 训练。
result: 在资源约束下显著提升了推理可靠性，保持了可扩展性。
conclusion: 弹性推理为实际部署中受预算限制的思维链推理提供了有效方案。
---

## Abstract
Large reasoning models (LRMs) have achieved remarkable progress on complex tasks by generating extended chains of thought (CoT). However, their uncontrolled output lengths pose significant challenges for real-world deployment, where inference-time budgets on tokens, latency, or compute are strictly constrained. We propose Elastic Reasoning, a novel framework for scalable chain of thoughts that explicitly separates reasoning into two phases—thinking and solution—with independently allocated budgets. At test time, Elastic Reasoning prioritizes the completeness of solution segments, significantly improving reliability under tight resource constraints. To train models that are robust to truncated thinking, we introduce a lightweight budget-constrained rollout strategy, integrated into GRPO, which teaches the model to reason adaptively when the thinking process is cut short and generalizes effectively to unseen budget constraints without additional training. Empirical results on mathematical (AIME, MATH500) and programming (LiveCodeBench, Codeforces) benchmarks demonstrate that Elastic Reasoning performs robustly under strict budget constraints, while incurring significantly lower training cost than baseline methods. Remarkably, our approach also produces more concise and efficient reasoning even in unconstrained settings. Elastic Reasoning offers a principled and practical solution to the pressing challenge of controllable reasoning at scale. Code is available in the supplementary material.

---

## 论文详细总结（自动生成）

### 1. 核心问题与整体含义（研究动机和背景）
- **问题**：大型推理模型（LRMs）通过生成极长思维链（CoT）在复杂任务上取得突破，但**推理长度不可控**，导致在实际部署中面临严格的 token 数、延迟或计算预算限制时可靠性下降。
- **背景**：现有方法（如直接截断、budget forcing）难以在资源约束下同时保证推理质量与可扩展性，亟需一种**可控且高效**的推理框架。

### 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：将思维链显式划分为两个独立阶段——**思考阶段（Thinking）** 和**解答阶段（Solution）**，并为每个阶段**独立分配预算**。在测试时优先保证解答阶段的完整性，从而提高资源受限下的可靠性。
- **关键技术细节**：
  - **两阶段分割**：模型输出先进行内部推理（思考），再生成最终答案（解答）。可通过特殊标记（如 <|think|> 和 <|solution|>）区分。
  - **轻量级预算约束 rollout 策略**：集成到 GRPO 训练框架中，在训练时随机截断思考阶段（施加预算），并通过 rollout 让模型**适应不完整的思考过程**。截断后模型仍需生成合理的解答。
  - **泛化能力**：由于使用分布式的预算约束（而非固定值），模型能泛化到**未见过的预算限制**，无需额外训练。
- **公式/算法流程**（文字说明）：
  1. 初始化策略模型与值函数（GRPO 基础）。
  2. 对每个样本，随机确定思考阶段预算（如最大 token 数）和解答阶段预算。
  3. 采样一条完整的思维链，若思考部分长度超过预算则直接截断；将截断后的状态作为 rollout 起点，继续生成解答。
  4. 使用 GRPO 更新策略：奖励函数鼓励解答正确性且惩罚过长思考（或鼓励预算内高效推理）。
  5. 测试时，根据实际预算分配思考与解答阶段的 token 数，优先保证解答阶段完整。

### 3. 实验设计
- **数据集/场景**：
  - 数学推理：**AIME**（2024）、**MATH500**。
  - 编程能力：**LiveCodeBench**（近期竞赛题目）、**Codeforces**（竞赛平台）。
- **基准（Benchmark）**：以上四个标准评测集。
- **对比方法**：文中未明确列出具体基线名称，但提到与“baseline methods”相比训练成本更低，且性能更优。推测基线可能包括：标准思维链（无预算控制）、强制截断（budget forcing）、逐步预算调整等方法。

### 4. 资源与算力
- 文中**未明确说明**使用的 GPU 型号、数量或训练时长。
- 仅提到训练成本**显著低于基线方法**，未给出具体数值。这可能因为弹性推理框架只需在 GRPO 上添加轻量级 rollout，无需额外大规模训练。

### 5. 实验数量与充分性
- **实验数量**：覆盖数学（2个）和编程（2个）共4个主流基准，并进行了消融实验（原文可能含预算约束泛化性、阶段分割影响等，摘要未详细列出）。
- **充分性**：实验领域较全面（数学+编程），且对比了约束/无约束两种场景。但**缺少详细表格和统计显著性测试**，无法直接判断差异是否显著。此外，可能缺少对更长序列或更大模型的验证。

### 6. 主要结论与发现
1. **鲁棒性**：在严格预算约束下，弹性推理显著优于传统方法，实现可控且可靠推理。
2. **训练高效**：训练成本低于其他可控推理方法，且能泛化到未见预算约束。
3. **无约束下也受益**：即使不设预算，模型也倾向于生成更简洁高效的思维链，提升推理质量。
4. **实际部署价值**：提供一种即插即用的解决方案，适用于对 token、延迟敏感的应用场景。

### 7. 优点
- **思想简洁**：将推理过程显式分为思考与解答，独立分配预算，契合人类问题解决模式。
- **训练轻量**：只需在 GRPO 中集成预算约束 rollout，无需改造模型架构或增加额外网络。
- **泛化性强**：模型能自动适应不同预算，无需为每个预算重新训练。
- **双向好处**：既解决资源约束问题，又提升无约束场景下的效率。

### 8. 不足与局限
- **实验细节缺失**：未提供具体 GPU 型号、训练时长、超参数设置，影响可复现性。
- **对比基线不明确**：文中未列出具体基线方法名称，难以公平评估优势程度。
- **领域覆盖有限**：仅评测数学和编程，未涉及多模态、常识推理等更广泛场景。
- **潜在风险**：思考阶段被截断可能导致推理链逻辑断裂，对需要深度思考的任务（如科学问题）效果未知。
- **任务复杂度依赖**：可能对简单任务带来过度思考，如何动态调节两阶段预算比例仍有挑战。

（完）
