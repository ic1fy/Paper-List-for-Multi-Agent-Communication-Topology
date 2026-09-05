# Paper List for LLM Multi-Agent Communication Topology

**大模型多智能体系统的通信拓扑：设计、优化与自适应**

*Communication Topology in LLM-Based Multi-Agent Systems: Design, Optimization, and Adaptation*

面向初学者的大模型多智能体通信与拓扑学习清单。

本版核对日期：2026-09-05。收录 13 篇 LLM 相关论文与 2 篇历史背景论文。它是精选入门清单，不是穷尽式综述；2026 年工作放在进阶部分，不把新预印本称为经典。

组织形式参考 [Paper-List-for-Prototypical-Learning](https://github.com/BeistMedAI/Paper-List-for-Prototypical-Learning) 的主题分类方式。各条目的解释为本清单独立整理。主体按“通信结构在何时、根据什么确定”分类；算法、规模与任务作为标签，阅读顺序另列。

## 目录

- [研究范围与群体智能](#scope)
- [主分类标准](#taxonomy)
- [I. 人工预设结构 · 拓扑设计](#fixed)
- [II. 提前优化、之后复用 · 拓扑优化](#offline)
- [III. 根据当前题目生成 · 任务自适应拓扑](#conditioned)
- [IV. 根据执行反馈调整 · 动态拓扑](#online)
- [附录：基础对照与评估](#evaluation)
- [附录：历史背景](#history)
- [智能体数量与模型规模](#scale)
- [阅读与复现实践路线](#roadmap)
- [以后如何添加论文](#template)

<a id="scope"></a>
## 研究范围与群体智能

核心问题：在给定任务、基础模型和预算下，怎样安排多个智能体的信息流，使整个系统更准确、更经济或更可靠？

节点在不同论文中含义不同：可能是一个带角色和独立上下文的智能体，也可能仅是一次 LLM 调用或一个工具操作。边通常表示信息可见性或依赖关系。比较论文前，先确认节点、边、轮次各自的定义。

它与群体智能有交集：都关注个体互动如何影响集体表现。传统群体智能尤其强调局部互动、分布式规则和自组织，Boids 是一个直观例子；LLM 协作也可能采用中央主管或统一训练的图设计器，因此不能把所有 LLM 多智能体系统都称为严格意义上的去中心化群体智能。[Reynolds 原文与作者说明](https://www.red3d.com/cwr/papers/1987/boids.html)

拓扑方法是在设计信息流；harness 是执行模型调用、工具、消息和状态管理的运行程序。harness 可以实现某种拓扑，也可以执行动态拓扑策略。

本清单以智能体之间的通信为主。单个模型内部的 attention、MoE 路由、GraphRAG 知识图谱不是同一个研究对象。

<a id="taxonomy"></a>
## 主分类标准

只用一条主轴：**对一道新题，通信结构在什么时候、依据什么被确定？**

| 主类 | 结构决定的时机与依据 | 代表工作 |
|---|---|---|
| I. 人工预设结构（拓扑设计） | 人提前规定连接，执行时沿用；实验可对比多种预设图 | Sparse MAD 的主要实验、MacNet 的拓扑比较 |
| II. 提前优化、之后复用（拓扑优化） | 在训练或校准题上搜索、学习或剪枝，供后续题目复用 | GPTSwarm 的连接优化、AgentPrune 的多查询剪枝设置 |
| III. 根据当前题目生成（任务自适应拓扑） | 看见一道新题后，为它生成通信结构 | Input Conditioned Graph Generation、G-Designer、ARG-Designer |
| IV. 根据执行反馈调整（动态拓扑） | 获得中间结果后，调整参与者、连接或继续通信的安排 | DyLAN、TodyComm |

这是按核心机制组织的阅读地图，不是互斥的学术定理。同一框架可能覆盖多个阶段：如 DyLAN 包含提前选队与运行时协作，AgentPrune 也有不同查询设置。每篇先放在最方便理解其主贡献的位置，并记录跨类机制。

GNN、强化学习、搜索和剪枝是“如何求解”的算法标签；智能体数与模型大小是实验设置；数学、代码与问答是任务标签；失败与预算分析是评估材料。它们不与上述四类并列。

每篇另记：优化节点/角色/边/消息/轮次中的哪些对象，以及是否存在中央控制器。

<a id="fixed"></a>
## I. 人工预设结构 · 拓扑设计

### I1. Improving Multi-Agent Debate with Sparse Communication Topology — Sparse MAD

- **作者 / 年份 / 发表**：Yunxuan Li 等；Findings of EMNLP 2024。
- **入口**：[正式论文](https://aclanthology.org/2024.findings-emnlp.427/) · [方法全文](https://arxiv.org/html/2406.11776)。
- **方法**：减少辩论中的同伴可见连接，比较不同密度的图。
- **阅读重点**：信息独立性、错误传播与成本之间的关系。
- **边界**：主要比较静态规则图，也有动态概率连接的探索；不能把“稀疏有益”解释为“边越少越好”。
- **建议**：第一篇拓扑机制复现，优先于直接训练 G-Designer。

### I2. Scaling Large Language Model-based Multi-Agent Collaboration — MacNet

- **作者 / 年份 / 发表**：Chen Qian 等；2024 首发，ICLR 2025（arXiv 页面确认录用）。
- **入口**：[论文](https://arxiv.org/abs/2406.07155)。
- **方法**：以有向无环图组织协作，比较结构与规模；作者报告了超过千个智能体的扩展实验。
- **阅读重点**：节点怎么计数、结构怎么生成、规模增加时质量与成本如何变化。
- **边界**：千级是规模研究案例，不是入门实验的默认配置；曲线不能外推成“无限增加智能体就一直变好”。
- **建议**：必读，适合理解网络结构与集体表现的关系。

<a id="offline"></a>
## II. 提前优化、之后复用 · 拓扑优化

### II1. GPTSwarm: Language Agents as Optimizable Graphs

- **作者 / 年份 / 发表**：Mingchen Zhuge 等；ICML 2024。
- **入口**：[正式论文](https://proceedings.mlr.press/v235/zhuge24a.html) · [作者代码](https://github.com/metauto-ai/GPTSwarm)。
- **方法**：把操作组织为计算图，并优化节点提示词及跨智能体连接。
- **阅读重点**：一个 agent 可以是一个子图；图节点不总等于一个完整智能体。
- **边界**：节点优化与边优化同时变化时，需要消融才能归因给拓扑。
- **建议**：必读，用来建立“图可以被优化”的概念。

### II2. Cut the Crap: An Economical Communication Pipeline for LLM-based Multi-Agent Systems — AgentPrune

- **作者 / 年份 / 发表**：Guibin Zhang 等；2024 首发，ICLR 2025。
- **入口**：[正式论文](https://proceedings.iclr.cc/paper_files/paper/2025/hash/bbc461518c59a2a8d64e70e2c38c4a0e-Abstract-Conference.html) · [全文](https://arxiv.org/html/2410.02506) · [作者代码](https://github.com/yanweiyue/AgentPrune)。
- **方法**：识别时空通信冗余，学习连接重要性后进行一次剪枝。
- **阅读重点**：同轮内信息流与跨轮消息流有何区别；剪枝优化成本如何摊销。
- **边界**：效率比较需同时说明优化阶段和最终推理阶段的开销。
- **建议**：必读；第二个可尝试的原方法复现。

<a id="conditioned"></a>
## III. 根据当前题目生成 · 任务自适应拓扑

### III1. Input Conditioned Graph Generation for Language Agents

- **作者 / 年份 / 状态**：Lukas Vierling、Jie Fu、Kai Chen；2024，按 arXiv 预印本收录，未确认正式会议信息。
- **入口**：[论文](https://arxiv.org/abs/2406.11555) · [作者代码](https://github.com/lukasVierling/DynamicGPTSwarm)。
- **方法**：基于图表示，通过强化学习微调一个生成连接的 LLM，使信息流依赖输入。
- **阅读重点**：和 G-Designer 比较“用 LLM 生成图”与“用图模型生成图”。
- **边界**：按输入变化不自动等于执行中逐轮重建网络。

### III2. G-Designer: Architecting Multi-agent Communication Topologies via Graph Neural Networks

- **作者 / 年份 / 发表**：Guibin Zhang 等；2024 首发，ICML 2025。
- **入口**：[正式论文](https://proceedings.mlr.press/v267/zhang25cu.html) · [方法全文](https://arxiv.org/html/2410.11782v3) · [作者代码](https://github.com/yanweiyue/GDesigner)。
- **方法**：编码智能体描述与任务，加入参考图；通过变分图自编码器生成通信结构，用任务反馈与正则训练设计器。
- **阅读重点**：区分文本编码器、图设计器和执行任务的大模型；区分按题生成与执行中调整。
- **边界**：任务效果无法直接通过大模型 API 反传，论文采用策略梯度。主要训练设计器；不是给执行大模型微调参数。
- **建议**：主线精读；先看 Figure 3、§3.2、§4、§5.1，再看 Appendix A 和代码。

### III3. Assemble Your Crew: Automatic Multi-agent Communication Topology Design via Autoregressive Graph Generation — ARG-Designer

- **作者 / 年份 / 发表**：Shiyuan Li 等；AAAI 2026。
- **入口**：[正式论文](https://ojs.aaai.org/index.php/AAAI/article/view/39481)。
- **方法**：根据任务逐步决定智能体数量、角色和连接。
- **阅读重点**：搜索空间从“现有节点之间连边”扩展到“成员与结构共同设计”。
- **边界**：角色、数量和边同时变化，单独归因给通信拓扑更困难。
- **建议**：读懂 G-Designer 后再读。

<a id="online"></a>
## IV. 根据执行反馈调整 · 动态拓扑

### IV1. A Dynamic LLM-Powered Agent Network for Task-Oriented Agent Collaboration — DyLAN

- **作者 / 年份 / 发表**：Zijun Liu 等；2023 首发，COLM 2024。
- **入口**：[论文](https://arxiv.org/abs/2310.02170) · [作者代码](https://github.com/SALT-NLP/DyLAN)。
- **方法**：通过智能体重要性评价选择团队，再进行动态任务协作。
- **阅读重点**：选择谁参与、何时停止，与直接学习任意通信边的区别。
- **边界**：读“dynamic”时要注明具体变化的对象，不能只靠标题分类。

### IV2. TodyComm: Task-Oriented Dynamic Communication for Multi-Round LLM-based Multi-Agent System

- **作者 / 年份 / 状态**：Wenzhe Fan 等；2026-02 首发、2026-05 修订，按 arXiv 预印本收录。
- **入口**：[论文](https://arxiv.org/abs/2602.03688)。
- **方法**：利用行为信息生成逐轮适应的通信拓扑，通过策略梯度优化任务效用。
- **阅读重点**：图从 G(Q) 变为依赖执行历史的 G_t(Q, history) 后，需要观察什么状态。
- **边界**：动态对抗和预算约束是其重要实验条件，迁移到普通任务需要再验证。
- **建议**：近期进阶阅读，不作为初学者第一篇。

<a id="evaluation"></a>
## 附录：基础对照与评估

### P1. Self-Consistency Improves Chain of Thought Reasoning in Language Models

- **作者 / 年份 / 发表**：Xuezhi Wang 等；2022 首发，ICLR 2023。
- **入口**：[论文](https://arxiv.org/abs/2203.11171)。
- **方法**：独立采样多条推理路径，再汇总一致答案。
- **为何收录**：不是多智能体通信论文，但它是判断“交流本身是否有效”的必要对照。
- **阅读重点**：多次采样的收益与智能体交流的收益如何区分。
- **边界**：不能把多次独立调用直接称为智能体之间的协作通信。

### P2. Improving Factuality and Reasoning in Language Models through Multiagent Debate

- **作者 / 年份 / 发表**：Yilun Du 等；2023 首发，ICML 2024。
- **入口**：[正式论文](https://proceedings.mlr.press/v235/du24e.html) · [arXiv](https://arxiv.org/abs/2305.14325)。
- **方法**：多个模型实例先作答，再读取同伴回答并多轮修正。
- **阅读重点**：一条来自同伴的消息具体怎样进入下轮提示词；多数一致为何不等于答案正确。
- **边界**：论文的改善基于具体模型与任务，不能直接推导为任意模型都适合辩论。
- **建议**：必读；适合先做机制复现。

### P3. Why Do Multi-Agent LLM Systems Fail? — MAST

- **作者 / 年份 / 发表**：Mert Cemri 等；NeurIPS 2025，Datasets and Benchmarks Track。
- **入口**：[正式论文](https://papers.nips.cc/paper_files/paper/2025/hash/b1041e52d3be19f0a9bc491657488e4a-Abstract-Datasets_and_Benchmarks_Track.html) · [arXiv](https://arxiv.org/abs/2503.13657)。
- **方法**：分析真实执行轨迹中的协作失败，建立分类体系。
- **阅读重点**：系统设计、智能体间目标或理解不一致、验证与终止问题。
- **边界**：失败分类不是某一种拓扑必然成功或失败的定理。
- **建议**：和性能提升论文一起读。

### P4. Single-Agent LLMs Outperform Multi-Agent Systems on Multi-Hop Reasoning Under Equal Thinking Token Budgets

- **作者 / 年份 / 状态**：Dat Tran、Douwe Kiela；2026-04，按 arXiv 预印本收录。
- **入口**：[论文](https://arxiv.org/abs/2604.02460)。
- **方法**：在思考 token 预算匹配条件下比较单智能体与多智能体。
- **阅读重点**：区分算力、上下文利用与协作结构的贡献。
- **边界**：结论针对所测多跳推理任务与模型，不能推广为所有多智能体系统都无效。
- **建议**：开始做性能对比前阅读。

<a id="history"></a>
## 附录：历史背景（选读）

### H1. Flocks, Herds, and Schools: A Distributed Behavioral Model

Craig W. Reynolds，SIGGRAPH 1987。[作者页面与原文](https://www.red3d.com/cwr/papers/1987/boids.html)。

理解局部规则与个体交互如何形成集体行为。它提供群体智能直觉，不是 LLM 通信的性能证据。

### H2. Collective dynamics of ‘small-world’ networks

Duncan J. Watts、Steven H. Strogatz，Nature 1998。[出版页](https://www.nature.com/articles/30918) · [Cornell 保存的原文](https://www.cs.cornell.edu/courses/cs6241/2019sp/readings/Watts-1998-smallworld.pdf)。

理解局部聚集与短路径如何同时出现。它不能证明小世界拓扑一定最适合 LLM，但能帮助阅读 MacNet 一类结构分析。

<a id="scale"></a>
## 智能体数量与模型规模

以下是具体论文设置，不是对全领域的统计。

| 工作 | 智能体数量 | 模型 | 查证位置 |
|---|---|---|---|
| Sparse MAD | 主实验 6；附录另测 4 | GPT-3.5；多模态 GPT-4 系列；对齐标注另有 Mistral 7B | [§3.1、§4.2、Appendix C](https://arxiv.org/html/2406.11776) |
| G-Designer | 主结果表多智能体方法使用 5；另测 5–20 的规模扩展 | gpt-4-1106-preview、gpt-3.5-turbo-0125 | [Table 1、§5.1、§5.2](https://arxiv.org/html/2410.11782v3) |
| AgentPrune | 框架整合实验包含 3、5；其他设置随任务而异 | gpt-3.5-turbo-0301、gpt-4-1106-preview | [Implementation Details、Appendix G.1.3](https://arxiv.org/html/2410.02506) |
| MacNet | 专门研究扩展，作者报告超过千个智能体 | 本版未提取其各实验模型配置 | [摘要](https://arxiv.org/abs/2406.07155) |

计数时另记汇总者、主管、工具节点是否算入 N；不要把角色数、总调用次数和并发数混为一谈。Sparse MAD 正文和多模态附录的模型名称粒度不同：复现具体表格时需按对应设置确认精确版本。

GPT 系列闭源模型在这些论文中用 API 名称标识；参数规模未公开披露，不能填写猜测的 B 数。B 表示十亿参数，例如 7B 约为 70 亿参数。

### 对初学者的配置建议（本清单建议，不是论文统一标准）

- **先做 3 个，再做 5 或 6 个**。3 个用于调通消息机制；比较密度、环与全连接时，4–6 个更有结构差异。研究无向环时尤其注意：3 个节点的环就是完全图。
- **先用一个固定版本的模型**。本地可以尝试 7B/8B 指令模型；如果目标任务几乎全错，再调整任务或换 14B/32B 等更强模型。参数量并不单独决定推理、纠错和工具能力。
- **模型必须先具备一定单体解题能力**。多个都不会解题的实例，不能保证通过交流获得正确解法。
- **多个智能体可以共用同一份模型权重或同一个 API 服务**，每个智能体保留独立提示词和历史。N 个智能体不要求 N 份权重常驻显存；并发会增加 KV cache、上下文和吞吐需求。
- **最后再做异构模型**。先隔离拓扑效果，再考虑强弱模型分别放在哪个位置。

### 成本不要只数智能体

若每个智能体每轮调用一次模型，N 个智能体、R 轮的主体生成调用约为 N×R；还需另计设计器、汇总者、重试和工具调用。

若每条有向边每轮各传递一条消息，全连接的有向消息关系是 N(N−1)：5 个节点为 20，10 个为 90。这不等于 API 调用数；同伴消息可能被合并进一次调用，但会增加输入 token。历史保留策略还会影响跨轮成本。

<a id="roadmap"></a>
## 阅读与复现实践路线

### 第一遍：先读六篇

Multiagent Debate → Sparse MAD → GPTSwarm → AgentPrune → G-Designer → MacNet。

Self-Consistency 作为基础对照插入；MAST 与预算匹配论文在开始报告性能结果前补读。

### 第一项实践：机制复现

目标：用同一个模型、同一批带标准答案的问题，对比独立作答投票、全连接辩论和稀疏辩论。

1. 用 3 个逻辑智能体调通消息历史，保存每轮输入输出。
2. 换成 4 或 6 个智能体，比较环与全连接；都先独立回答，再交流。
3. 先固定轮数和每次输出上限，记录质量与实际 token；再补充总预算匹配的比较。相同轮数并不等于相同 token。
4. 从小规模试运行开始；正式结论增加独立题目和重复运行，报告不确定性。
5. 标注正确改错、错误改对、盲目一致等轨迹，解释为什么一张图更好。

这是理解 Sparse MAD 的简化实验；若更换了模型、数据或提示词，应称为机制复现或迁移验证，不能宣称复现了原论文分数。

### 第二项实践：优化已有图

先尝试随机删边或逐条删边，在验证集比较；把它作为学习练习和对照，再复现 AgentPrune 的学习与剪枝方法。测试集保持独立。

### 第三项实践：学习生成图

补齐文本 embedding、有向图与邻接矩阵、GCN、VAE/VGAE 和策略梯度，再读 G-Designer 代码。分别复现图生成、任务执行、任务反馈训练，检查每个模块是否改变了实际消息可见性。

G-Designer 的学习重点是：一个较小的设计器根据任务生成图，执行模型负责答题；训练设计器与训练执行模型是两件事。

### 第四项实践：扩展到动态协作

读 DyLAN、TodyComm，研究运行中选择参与者、通信对象或停止轮次。和固定图、按题生成图、主管按需调用三类对照比较，计入调度开销。

<a id="template"></a>
## 以后如何添加论文

每篇保留如下信息；没有查证的地方写“未核对”，不要补猜测值。

```text
标题 / 作者 / arXiv 首发年份 / 正式会议年份：
论文链接 / 作者代码链接：
主类 I/II/III/IV（按结构决定的时机；可标跨类）：
节点、边与轮次分别代表什么：
拓扑何时变化：
优化对象与算法：
节点数（是否包含主管、汇总者、工具节点）：
执行模型及精确版本 / 已公开参数量：
设计器模型 / 是否训练执行模型：
任务、数据划分与重复次数：
公平对照 / 输入、输出及思考 token / 延迟：
论文实际支持的结论：
限制与想验证的问题：
阅读状态 / 复现状态：
```

维护原则：正式出版信息优先；预印本单独标注；代码链接只表示作者提供，不表示本清单已运行验证。不能从某一种架构被称为“动态”就推断其逐轮改边，也不能从更多智能体提升正确率就直接推断存在等预算的协作收益。
