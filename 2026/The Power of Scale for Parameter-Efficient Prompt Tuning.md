这篇论文提出了一种名为**Prompt Tuning（提示微调）**的方法，旨在通过极少量的可训练参数高效地适配冻结的预训练语言模型。以下是该论文方法论的详细总结：

### 1. 核心机制：软提示微调 (Soft Prompt Tuning)
与传统的全量微调（Fine-tuning）不同，Prompt Tuning 保持预训练模型的所有权重（$\theta$）**冻结**，仅在输入层引入一组可学习的连续向量，称为“软提示”（Soft Prompts, $\theta_P$）[<sup>1</sup>](https://arxiv.org/pdf/2104.08691)。
*   **输入构造**：给定输入 token 序列 $X$，模型首先将其转换为嵌入矩阵 $X_e$。Prompt Tuning 将可学习的提示矩阵 $P_e$ 拼接到 $X_e$ 之前，形成 $[P_e; X_e]$，然后送入冻结的编码器 - 解码器模型 [<sup>2</sup>](https://aclanthology.org/2021.emnlp-main.243/)。
*   **训练过程**：通过反向传播仅更新提示参数 $\theta_P$，模型主体参数 $\theta$ 保持不变。这使得该方法可以被视为对 GPT-3 离散文本提示（Prompt Design）的端到端学习简化版 [<sup>3</sup>](https://liner.com/review/power-scale-for-parameterefficient-prompt-tuning)。

### 2. 关键设计选择 (Design Decisions)
论文对 Prompt Tuning 的多个超参数进行了消融实验，确定了以下最佳实践：
*   **提示初始化 (Initialization)**：比较了随机初始化、从词汇表中采样初始化以及**类标签初始化 (Class Label Initialization)**。实验表明，使用下游任务类别标签的嵌入来初始化提示 token 效果最好，尤其是在中小模型上；但在超大模型（如 T5-XXL）上，初始化策略的影响会减弱 [<sup>1</sup>](https://arxiv.org/pdf/2104.08691)。
*   **提示长度 (Prompt Length)**：提示长度可变（从 1 到 150 个 token）。虽然增加长度通常能提升性能，但研究发现随着模型规模扩大，即使是很短的提示（甚至单 token）也能在 XXL 模型上取得良好效果，表明大模型需要更少的条件信号 [<sup>2</sup>](https://aclanthology.org/2021.emnlp-main.243/)。
*   **参数效率**：该方法极度节省参数。例如在 T5-XXL（110 亿参数）上，每个任务仅需约 20,480 个可训练参数（假设提示长度为 5），相比全量微调减少了超过 5 个数量级 [<sup>4</sup>](https://paperswithcode.com/paper/the-power-of-scale-for-parameter-efficient)。

### 3. 预训练目标调整：LM Adaptation
由于实验主要基于 T5 模型，而 T5 原始预训练使用的是“跨度损坏”（Span Corruption）目标（包含特殊 sentinel 标记），这不利于自然文本的提示生成。
*   **问题**：直接使用原始 T5  checkpoint 进行 Prompt Tuning 效果不稳定，尤其是中等规模模型。
*   **解决方案**：作者提出**LM Adaptation**。在 Prompt Tuning 之前，将 T5 模型在纯文本数据上继续预训练 100k 步，使用标准的语言建模（LM）目标（即预测下一个自然 token）。这一步只需执行一次，生成的冻结模型可用于多个下游任务，显著提升了 Prompt Tuning 的鲁棒性和性能 [<sup>1</sup>](https://arxiv.org/pdf/2104.08691)。

### 4. 提示集成 (Prompt Ensembling)
论文提出了一种高效的集成学习方法：
*   **方法**：对同一个下游任务，在同一个冻结模型上训练 $N$ 个不同的提示（Prompts）。
*   **推理**：推理时，将同一个输入复制 $N$ 份，分别与 $N$ 个提示组成 batch 并行通过模型，最后通过投票得出结果。
*   **优势**：相比传统的模型集成（需要存储和运行 $N$ 个大模型），Prompt 集成只需存储 $N$ 个小提示向量，且推理时只需加载一次主模型，极大地降低了存储和计算成本 [<sup>2</sup>](https://aclanthology.org/2021.emnlp-main.243/)。

### 5. 规模效应 (The Power of Scale)
该方法论的一个核心发现是性能与模型规模强相关：
*   **小模型**：Prompt Tuning 的表现不如全量微调。
*   **大模型**：随着模型参数量超过 10 亿（1B），Prompt Tuning 的性能迅速逼近全量微调。在 T5-XXL（11B）规模上，Prompt Tuning 在 SuperGLUE 基准上的表现已与全量微调相当，且显著优于 GPT-3 的少样本学习（Few-shot Learning）[<sup>3</sup>](https://liner.com/review/power-scale-for-parameterefficient-prompt-tuning)。

### 6. 鲁棒性与领域迁移
由于冻结了主体模型参数，Prompt Tuning 减少了模型对特定训练数据中虚假相关性（Spurious Correlations）的过拟合。实验显示，在领域迁移（Domain Shift）任务中（如从 SQuAD 迁移到其他 QA 数据集），Prompt Tuning 的零样本泛化能力优于全量微调 [<sup>1</sup>](https://arxiv.org/pdf/2104.08691)。