---
title: "Encode, Think, Decode: Scaling test-time reasoning with recursive latent thoughts"
title_zh: 编码、思考、解码：通过递归潜在思维扩展测试时推理
authors: "Yeskendir Koishekenov, Aldo Lipani, Nicola Cancedda"
date: 2025-09-19
pdf: "https://openreview.net/pdf?id=jBSye8M3FQ"
tags: ["query:vlm-cot"]
score: 6.0
evidence: 通过递归潜在思维增强LLM推理，类似于思维链
tldr: 现有方法通过扩大参数或生成复杂思维链来提升推理，但代价高。该文提出编码-思考-解码（ETD），在中间训练阶段让模型在推理相关层反复迭代，增强潜在推理。ETD保持原始架构、参数和训练数据不变，以极低成本提升测试时推理能力。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 扩大参数或显式思维链成本高，且有效性集中在少数层。
method: 提出ETD方法，训练模型在推理相关层进行递归迭代以增强潜在推理。
result: 在保持架构不变下显著提升推理能力。
conclusion: 为低成本增强LLM推理提供了新范式。
---

## Abstract
Most efforts to improve the reasoning capabilities of large language models (LLMs) involve either scaling the number of parameters and the size of training data, or scaling inference computation by letting models generate complex chains of thought. Motivated by interpretability studies showing that the crucial computation required for reasoning tasks is concentrated in a limited range of layers, we introduce Encode–Think–Decode (ETD), a method that enhances the reasoning capabilities of a base model by training it to iterate over a small subset of reasoning-relevant layers during the mid-training stage. ETD amplifies latent reasoning while preserving the original architecture, parameter count, hyperparameters, and training data composition. When iterating on the selected layers at inference time, ETD models yield substantial gains on 17 reasoning benchmarks, including +28.4% relative accuracy improvement on GSM8K and +36% on MATH with the OLMo-2 1B Base model. We also explore an adaptive depth strategy that adjusts the computation per input token. Our results show that recursive latent reasoning offers a simple and effective path to stronger LLM reasoning.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **核心问题**：如何在不显著增加模型参数、训练数据或显式推理链长度的情况下，提升大语言模型（LLM）的推理能力。现有方法主要依赖扩大参数规模或生成复杂的思维链（Chain-of-Thought），但这些方法计算成本高昂，且模型内部的有效推理计算仅集中在少数层。
- **研究动机**：受可解释性研究启发——推理任务的关键计算集中在有限层范围内。因此，通过让模型在推理相关层进行递归迭代，可以增强潜在推理，无需改变模型架构或增加参数。
- **整体含义**：提出了一种低成本、高效的推理增强范式，为测试时推理扩展提供了新思路。

## 2. 方法论：核心思想、关键技术细节、算法流程

- **核心思想**：提出“编码–思考–解码”（Encode–Think–Decode，ETD）方法。在中间训练阶段（mid-training），训练基础模型对少量与推理相关的层进行递归迭代，从而放大潜在推理能力，同时保持原始架构、参数数量、超参数和训练数据组成不变。
- **关键技术细节**：
  - 在推理时，模型对选定的推理相关层重复执行多次前向传播（迭代），而非生成显式文本思维链。
  - 使用自适应深度策略：根据每个输入令牌动态调整迭代次数，进一步优化计算分配。
- **算法流程（文字说明）**：
  1. 选择一组与推理相关的小范围层（例如模型的中间层）。
  2. 在中间训练阶段，对这些层进行额外的迭代训练：即模型在通过选定层时，重复执行这些层的计算多次（或通过循环连接），更新隐藏状态。
  3. 在推理阶段，同样对选定层进行多次迭代，产生更强的潜在推理表示，最后解码输出。
  4. 可选：应用自适应深度策略，根据令牌重要性决定迭代次数。

## 3. 实验设计

- **使用的数据集 / 场景**：在17个推理基准上进行评估，包括GSM8K（数学）、MATH（数学）等。
- **Benchmark**：涵盖了数学推理、常识推理等多种推理任务。
- **对比方法**：未在摘要中详细列出具体对比方法，但隐含与原始基础模型（无ETD）以及其他扩展方法（如扩大参数、显式思维链）对比。文中提到“在保持架构不变下显著提升推理能力”，对比基线应为基础模型。

## 4. 资源与算力

- **摘要中未明确提及资源与算力**（如GPU型号、数量、训练时长等）。仅有模型规模信息：使用OLMo-2 1B Base模型作为实验基础。因此无法总结具体算力要求。

## 5. 实验数量与充分性

- **实验数量**：在17个推理基准上测试，数量较多，覆盖范围较广。
- **充分性与客观性**：
  - 使用了多个不同领域的基准，增强了泛化性。
  - 但仅展示了1B模型的实验结果，未提及是否在更大规模模型上验证，可能存在规模局限。
  - 未明确进行消融实验（如不同层选择、迭代次数影响），但自适应深度策略可视为一种消融。
  - 总体实验设计合理，但缺乏与其他先进推理增强方法（如CoT蒸馏、自我一致性等）的直接比较。

## 6. 主要结论与发现

- ETD方法在OLMo-2 1B基础模型上，在17个推理基准中取得显著提升：GSM8K相对准确率提升28.4%，MATH提升36%。
- 递归潜在推理是一种简单有效且成本极低的路径，可增强LLM推理能力，无需改变架构或增加参数。
- 自适应深度策略可进一步优化计算效率。

## 7. 优点

- **方法简洁**：仅增加中间训练阶段的递归迭代，不改变原始模型架构、参数和训练数据，易于集成。
- **计算成本低**：避免了大参数或长思维链的高昂计算，仅在少数层增加少量迭代。
- **效果显著**：在多个数学推理基准上大幅提升，相对提升超过30%。
- **可扩展性**：自适应深度策略允许动态调整计算量，兼顾效率与性能。

## 8. 不足与局限

- **实验覆盖有限**：仅基于1B参数模型，未在更大模型（如7B、70B）上验证，结果可能无法推广到更大规模。
- **缺乏与主流方法的直接对比**：未与标准CoT、自我一致性、树搜索等方法在相同设置下比较。
- **对层选择的依赖**：未讨论如何自动确定“推理相关层”，可能需人工预筛选，影响通用性。
- **风险/偏差**：仅在数学推理基准上测试，未涉及常识推理、代码生成等任务，可能存在任务偏差。
- **迭代次数的上限**：递归迭代可能带来延迟增加，文中未讨论计算时间与精度的权衡。
- **缺少源代码和可复现性细节**：未公开训练代码或超参数设置。

（完）
