这篇文章提出了一种名为 **HoarePrompt** 的方法，旨在利用大语言模型（LLM）验证程序是否符合自然语言需求。其方法论核心是将程序验证中的 **Hoare 逻辑** 与 **最强后条件（SP）计算** 思想适配到自然语言语境中。

主要技术贡献与流程如下：

1.  **自然 Hoare 三元组（Natural Hoare Triple）**：
    传统 Hoare 三元组使用形式化逻辑，而 HoarePrompt 使用自然语言表达前置条件（Pre）和后置条件（Post）。定义了 **自然最强后条件（NSP）**，通过 LLM 模型 $SP(Post|Pre, C)$ 计算代码片段 $C$ 在给定前置条件下的可达状态描述。

2.  **结构化状态传播**：
    方法首先从需求中提取前置条件，然后沿程序控制流图（CFG）遍历。对赋值、分支（if-else）、异常处理及输入流等语句，逐步采样 NSP，生成关键程序点的自然语言状态描述，并将这些描述作为注释添加到代码中。

3.  **few-shot-driven k-induction（循环处理）**：
    针对循环语义推理难点，提出了一种改进的 k-归纳法。将循环展开 $k$ 次（实验最优 $k=3$）， sequentially 计算每次迭代后的 NSP。利用这些迭代三元组作为 **few-shot 示例**，引导 LLM 归纳推断整个循环的最终后条件，显著提升了循环语义的总结精度。

4.  **正确性分类与评估**：
    利用 annotated 代码（包含状态描述）提示 LLM 判断程序是否满足需求。为评估效果，作者构建了 **CoCoClaNeL** 数据集（基于 2024 年 Codeforces 题目），实验显示 HoarePrompt 在 Matthews Correlation Coefficient (MCC) 上比零-shot-CoT 和基于测试的分类方法分别提升了 61% 和 106%。