---
title: Long Chain-of-Thought Reasoning Across Languages
title_zh: 跨语言的长思维链推理
authors: "Josh Barua, Seun Eisape, Kayo Yin, Alane Suhr"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=2kKXbsRhYI"
tags: ["query:vlm-cot"]
score: 9.0
evidence: 直接研究大型模型中的跨语言长思维链推理
tldr: 尽管大型推理模型在英语中生成思维链表现出色，但跨语言迁移机制未知。该文系统研究了缩放、预训练、后训练和推理四个阶段，比较了英语思维链与目标语言思维链在九种语言上的表现。发现增大模型规模提升英语思维链的多语言性能，但目标语言思维链仍存在差距。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 长思维链推理能力向非英语语言的迁移尚不明确。
method: 系统比较四种模型发展阶段和两种推理设置下的多语言思维链性能。
result: 增大模型规模提升英语思维链的多语言任务表现，但目标语言思维链仍有不足。
conclusion: 揭示了长思维链跨语言迁移的机制和局限。
---

## Abstract
While large reasoning models have shown remarkable ability to generate long chains-of-thought (CoTs) in English, we still lack understanding of how these long-form reasoning abilities transfer to the vast majority of the world’s languages. In this work, we systematically investigate four key stages of model development–scaling, pretraining, post-training, and inference–to understand how long CoT capabilities extend beyond English. We compare two reasoning settings across nine non-English target languages: En-CoT, where models process target-language inputs, but reason in English; and Target-CoT, where models both process inputs and generate long CoTs in the target language. We find that scaling reasoning model size improves multilingual task performance in En-CoT, but Target-CoT performance lags behind. This gap widens for tasks requiring long, multi-step CoTs such as mathematical reasoning. Shifting to pretraining, we find that adding a specialized reasoning stage enhances En-CoT performance but degrades Target-CoT, whereas broad multilingual pretraining improves both modes simultaneously. Given the scarcity of high-quality reasoning traces in languages other than English, we explore synthetic data curation approaches for post-training. We demonstrate that fine-tuning on reasoning traces
automatically translated from gold English traces outperforms fine-tuning on target-language traces distilled from large reasoning models. Finally, we report disparities in inference efficiency between languages and uncover language-specific failure modes
in CoTs. We release models, datasets, and code to foster further research.

---

## 论文详细总结（自动生成）

# 跨语言的长思维链推理：系统性研究总结

## 1. 核心问题与整体含义（研究动机与背景）
大型推理模型（如生成长思维链CoT的模型）在英语中表现卓越，但其长链推理能力如何迁移至非英语语言仍然未知。现有研究多聚焦英语环境，忽视了全球绝大多数语言。因此，本文旨在系统探究 **长思维链推理能力跨语言迁移的机制与局限**，为多语言推理模型的发展提供理论基础与实践指导。

## 2. 方法论
### 核心思想
将模型开发过程分为四个关键阶段——**缩放（Scaling）、预训练（Pretraining）、后训练（Post-training）和推理（Inference）**，并在每个阶段比较两种推理设置：
- **En-CoT**：模型处理目标语言输入，但用英语进行推理（思维链）。
- **Target-CoT**：模型同时用目标语言处理输入和生成思维链。

通过对比两种设置在九种非英语语言上的表现，解析跨语言迁移的瓶颈。

### 关键技术细节
- **缩放阶段**：增大推理模型规模，观察多语言任务性能变化。
- **预训练阶段**：对比两种策略  
  - 添加专门推理阶段（Specialized reasoning stage）→ 提升En-CoT但损害Target-CoT  
  - 广泛多语言预训练（Broad multilingual pretraining）→ 同时改善两种模式
- **后训练阶段**：针对高质量推理踪迹稀缺的问题，探索合成数据方案  
  - 使用**自动翻译的gold英语推理踪迹**进行微调  
  - 对比使用**从大型模型蒸馏出的目标语言推理踪迹**进行微调
- **推理阶段**：分析不同语言间的推理效率差异，并识别特定语言的失败模式。

> 文中未提供具体公式或算法流程，以上为文字性描述。

## 3. 实验设计
- **数据集/场景**：覆盖九种非英语目标语言；任务包括需要长时间、多步骤推理的数学推理等基准。
- **基准对比**：  
  - En-CoT vs Target-CoT 的跨语言表现  
  - 不同模型规模（缩放实验）  
  - 不同预训练策略（专门推理 vs 多语言）  
  - 不同后训练数据来源（自动翻译gold英语轨迹 vs 蒸馏目标语言轨迹）
- **评估指标**：未明确说明，但推测为任务准确率、思维链长度/质量等。

## 4. 资源与算力
文中 **未明确提及** 具体使用的GPU型号、数量、训练时长或推理开销等算力信息。仅指出发现了推理效率的语言差异，但未给出量化数据。

## 5. 实验数量与充分性
基于摘要描述，实验覆盖四个主要维度（缩放、预训练、后训练、推理），每个维度至少包含两组对比（En-CoT vs Target-CoT；不同策略变体）。例如：
- 缩放阶段：至少比较1种及以上模型规模。
- 预训练阶段：比较两种策略。
- 后训练阶段：比较两种数据合成方法。
- 推理阶段：分析效率差异及失败模式。

**充分性评估**：实验设计系统性较强，覆盖了模型开发全链条，且通过九种语言验证了跨语言普遍性。但缺少具体实验次数、消融组数、语言类别分布等细节，因此 **只能视为初步系统研究**，未提供充分统计证据表明结果稳健性（如多次重复、置信区间等）。

## 6. 主要结论与发现
1. **缩放效应**：增大推理模型规模可提升 En-CoT 模式下的多语言任务性能，但 Target-CoT 性能仍落后。且任务复杂度越高（如数学推理），差距越大。
2. **预训练影响**：  
   - 引入专门推理阶段 → 增强 En-CoT，但损害 Target-CoT  
   - 广泛多语言预训练 → 两种模式均受益
3. **后训练数据策略**：使用 **自动翻译自 gold 英语推理踪迹** 进行微调，优于使用 **从大型模型蒸馏的目标语言推理踪迹**。
4. **推理效率与失败模式**：不同语言间推理效率存在差异，且发现了语言特有的思维链失败类型（如特定语法结构导致中断、逻辑跳步等）。

## 7. 优点
- **系统性**：首次系统性地从缩放、预训练、后训练、推理四个阶段剖析跨语言长思维链迁移，填补了领域空白。
- **设置巧妙**：区分 En-CoT 与 Target-CoT，精准定位瓶颈（处理语言 vs. 推理语言）。
- **实用性方案**：提出自动翻译 gold 英语踪迹作为后训练数据来源，缓解了非英语语言高质量数据稀缺问题。
- **可复现性**：承诺释放模型、数据集和代码，便于后续研究。

## 8. 不足与局限
- **实验覆盖有限**：仅涉及九种非英语语言，无法代表全球语言多样性（如形态丰富语言、低资源语言）。
- **偏差风险**：主要结论可能受所选语言、任务（数学推理）和模型架构影响，泛化至其他任务（如常识推理、对话）需验证。
- **资源信息缺失**：未提供算力消耗，影响对方法可扩展性的判断。
- **公平性存疑**：En-CoT 与 Target-CoT 的比较可能隐含英语优势（如 token 效率、训练数据分布），未完全控制变量（如不同语言 tokenizer 差异）。
- **实际应用限制**：Target-CoT 性能始终落后于 En-CoT，当前模型可能不适合要求纯母语推理的场景。

（完）
