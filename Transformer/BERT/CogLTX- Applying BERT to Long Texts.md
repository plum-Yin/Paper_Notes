# Abstract

BERT无法处理长文本，因为它的内存和时间消耗呈二次增长。解决这个问题的最自然的方法，比如通过滑动窗口对文本进行切片或简化转换器，都没有足够的远程关注，或者需要定制CUDA内核。

BERT的最大长度限制提醒了我们人类工作记忆的有限容量(5 ~ 9个块)——那么人类是如何认知长文本的呢?基于Baddeley[2]的认知理论，

提出的CogLTX 框架通过训练一个判断模型来识别关键句子，并将它们连接起来进行推理，并通过**排练和衰减**实现多步推理。由于相关性注释通常不可用，我们建议使用**干预**来创建监督。

作为一种通用算法，CogLTX在各种下游任务上的性能优于或获得与SOTA模型相当的结果，其内存开销与文本长度无关。



---

Baddeley[2]在其1992年的经典著作中指出:“中央执行——(工作记忆)系统的核心，负责协调(多模式)信息”，“功能类似于一个有限容量的注意力系统，能够选择和操作控制过程和策略”。后来的研究详细说明，工作记忆中的内容会随着时间的推移而衰减[5]，除非通过 rehearsal[3]来保存，即注意并刷新大脑中的信息。然后，被忽略的信息通过retrieval competition不断与长期记忆中的相关项目更新[52]，在工作记忆中收集足够的信息进行推理。

BERT和工作记忆之间的类比启发我们使用CogLTX框架来像人类一样识别长文本。CogLTX背后的基本理念相当简洁——<u>通过关键句子的串联进行推理</u>(图2)——而紧凑的设计则需要弥合机器和人类推理过程之间的差距。

MemRecall is the process to extract key text blocks z from the long text $\rm \mathbf{x}$. Then $\rm\mathbf{z}$ is sent to the BERT, termed reasoner, to fulfill the specific task.

---

CogLTX的关键步骤是MemRecall，即通过将文本块视为episodic memories来识别相关文本块的过程。

MemRecall模拟工作记忆的检索竞争、排练和衰退，促进多步骤推理。

引入另一个BERT，称为judge，对块的相关性进行评分，并与原始 BERT reasoner 联合训练。此外，CogLTX可以通过干预，将task-oriented的标签转换为相关注释来训练 judge。

我们的实验表明，CogLTX在四个任务(包括NewsQA [44]， HotpotQA [53]， 20NewsGroups[22] 和 Alibaba)上的性能优于或达到了与最先进的性能相当的性能，并且无论文本长度如何，内存消耗都是恒定的。

---

