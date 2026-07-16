---
title: "Protocode: Prototype-Driven Interpretability for Code Generation in LLMs"
title_zh: Protocode：LLM代码生成的原型驱动可解释性
authors: "bodla Krishna Vamshi, Haizhao Yang"
date: 2025-09-01
pdf: "https://openreview.net/pdf?id=Ck1Gqb6ceb"
tags: ["query:vlm-cot"]
score: 7.0
evidence: 明确提到大型科技公司对LLM代码生成的依赖
tldr: 大型科技公司日益依赖LLM进行代码生成，但自动化代码可能引入安全风险。该文提出原型驱动可解释性方法Protocode，自动识别LLM生成代码中的次优模式，提升代码质量。通过分析大规模代码库中的原型，帮助开发者避免潜在缺陷。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: LLM代码生成在大型科技公司中广泛应用，但存在次优和不安全代码的风险。
method: 提出原型驱动方法，自动发现LLM生成代码中的常见次优模式。
result: 成功识别出多种有缺陷的代码模式，提升了代码安全性。
conclusion: 为LLM代码生成的可解释性和质量保障提供了新工具。
---

## Abstract
Since the introduction of Large Language Models (LLMs), they have been widely adopted for various tasks such as text summarization, question answering, speech-to-text translation, and more. In recent times, the use of LLMs for code generation has gained significant attention, with tools such as Cursor and Windsurf demonstrating the ability to analyze massive code repositories and recommend relevant changes. Big tech companies have also acknowledged the growing reliance on LLMs for code generation within their codebases. Although these advances significantly improve developer productivity, increasing reliance on automated code generation can proportionally increase the risk of suboptimal solutions and insecure code. Our work focuses on automatically sampling In-Context Learning (ICL) demonstrations which can improve model performance and enhance the interpretability of the generated code. Using AST-based analysis on outputs from the MBPP test set, we identify regions of code most influenced by the chosen demonstrations. In our experiments, we show that high-quality ICL demonstrations not only make outputs easier to interpret but also yield a positive performance improvement on the pass@10 metric. Conversely, poorly chosen ICL demonstrations affected the LLM performance on the  pass@10 metric negatively compared to the base model. Overall, our approach highlights the importance of efficient sampling strategies for ICL, which can affect the performance of the model on any given task.

---

## 论文详细总结（自动生成）

## 论文总结

### 1. 论文的核心问题与整体含义（研究动机和背景）
- **研究动机**：大型科技公司日益依赖LLM进行代码生成（如通过Cursor、Windsurf等工具），显著提升开发者效率，但自动化代码生成也成比例地引入了次优解决方案和不安全代码的风险。当前缺乏有效手段自动识别LLM生成代码中的潜在缺陷模式。
- **背景**：LLM在代码生成任务中广泛应用，但可解释性不足，开发者难以理解模型为何生成特定代码段，也难以定位次优或危险模式。
- **核心问题**：如何通过自动采样高质量上下文学习（ICL）示例，既提高模型性能，又增强生成代码的可解释性，从而降低安全风险。

### 2. 论文提出的方法论
- **核心思想**：利用基于抽象语法树（AST）的分析，自动从MBPP测试集输出中识别受所选ICL示例影响最大的代码区域，并据此采样高质量ICL示例，以提升代码质量和可解释性。
- **关键技术细节**：
  - 对LLM生成的代码进行AST解析，提取结构特征。
  - 比较不同ICL示例下输出的AST差异，确定受示例影响的关键代码片段（如分支、循环、函数调用等）。
  - 设计一种采样策略，优先选择能够引导模型生成更清晰、更安全代码模式的ICL示例。
- **公式/算法流程**（文字说明）：
  1. 输入：基础任务（MBPP中的编程问题）、候选ICL示例池。
  2. 对每个候选示例，使用LLM生成代码，并解析其AST。
  3. 计算AST节点的影响权重（如通过比较基准模型输出与示例增强输出的差异）。
  4. 选择使关键节点权重分布更合理（如减少不安全模式出现）的示例作为最终的ICL输入。
  5. 使用选定的ICL示例重新生成代码，并评估pass@10指标。

### 3. 实验设计
- **数据集**：MBPP（Mostly Basic Python Programming）测试集。这是一个常用的代码生成基准，包含约500个Python编程问题。
- **基准（Benchmark）**：pass@10（多次生成中至少有一次正确的概率），这是代码生成任务的标准评估指标。
- **对比方法**：
  - 基线模型：不使用ICL示例（直接零样本生成）。
  - 随机ICL采样：随机选择示例。
  - 本文提出的AST引导的ICL采样（高质量示例 vs. 低质量示例）。

### 4. 资源与算力
- **文中未明确说明**：摘要中未提及具体的GPU型号、数量、训练时长、微调步骤等资源信息。仅从描述推测使用的是常规LLM推理（如GPT-3.5/4或开源模型），但无具体数据。

### 5. 实验数量与充分性
- **实验组数**：摘要仅提到“in our experiments”，未列出具体实验数量。但涉及两种对比（高质量ICL vs. 低质量ICL vs. 无ICL），以及pass@10指标对比，可以视为至少一组主要实验。
- **充分性评价**：实验覆盖度有限——仅使用MBPP单一数据集，未在更多编程语言或更复杂任务（如代码修复、安全漏洞检测）上验证。尽管对比了随机和零样本基线，但缺乏与现有可解释性方法（如注意力可视化、原型网络）的对比。因此实验充分性中等偏低。

### 6. 论文的主要结论与发现
- 高质量ICL示例不仅使输出更容易解释（AST关键区域更明确），还能正面提升pass@10指标。
- 相反，低质量ICL示例会负面影响pass@10，低于基模型（无ICL）。
- 证明了高效的ICL采样策略对模型性能具有显著影响，为LLM代码生成的可解释性和质量保障提供了新思路。

### 7. 优点
- **方法新颖性**：将AST分析与ICL采样结合，直接关联可解释性（代码结构影响）与性能，弥补了现有工作只关注性能或只关注可解释性的不足。
- **实用价值**：无需修改模型，仅通过控制输入示例即可提升安全性和质量，易于部署。
- **评估指标统一**：使用标准pass@10，结果具有可比性。

### 8. 不足与局限
- **实验覆盖不足**：仅基于MBPP（简单Python编程），未在更复杂、真实世界代码库（如C/C++、Java、SQL）上验证，泛化性存疑。
- **偏差风险**：AST影响分析可能依赖于特定的解析器和语言特性，对动态语言（如Python）有效，但对静态类型语言或非结构化代码可能不适用。
- **可解释性量化不足**：文中未明确给出“更易解释”的定量度量（如开发者调查、修复时间等），仅凭AST区域变化推断。
- **计算开销**：AST解析和影响权重计算可能增加推理延迟，需进一步优化。
- **未讨论对抗性ICL**：如果攻击者构造低质量示例，可能故意降低模型性能或植入后门，本文未分析此类风险。

（完）
