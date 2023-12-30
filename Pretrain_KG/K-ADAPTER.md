# Basic Information:

- Title: K-ADAPTER: Infusing Knowledge into Pre-Trained Models with Adapters (K-ADAPTER:使用适配器将知识注入预训练模型)
- Authors: Ruize Wang, Duyu Tang, Nan Duan, Zhongyu Wei, Xuanjing Huang, Jianshu Ji, Guihong Cao, Daxin Jiang, Ming Zhou
- Affiliation: Fudan University, Shanghai, China (第一作者所属机构：复旦大学)
- Keywords: pre-trained models, adapters, knowledge infusion, factual knowledge, linguistic knowledge
- URLs: Paper: https://arxiv.org/abs/2002.01808v5 , GitHub code: https://github.com/microsoft/k-adapter

# 摘要:

- a.本文的研究背景:
  - 本文旨在利用K-ADAPTER框架注入多种类型的先前知识到预训练模型中，同时保留原始参数。
- b.过去的方法、问题及其动因：
  - 过去的方法注入先前的知识时直接改变原始预训练的模型，导致训练效果下降，K-ADAPTER通过实用adapter，使得注入知识简单、高效。
- c.本文提出的研究方法：
  - 本文提出了K-ADAPTER，这是一个支持多种类型的注入知识到预训练模型的框架，可以整合称为adapter的有效神经模型。adapter是知识特定的模型，它们被插到预训练模型之外，对不同类型的注入知识导出不同的表示形式，训练效果比预训练模型简单快捷。
- d.本文方法在任务及其性能上的表现：
  - K-ADAPTER在三个基于知识的任务中展示出了极高的性能，每种类型的adapter的训练参数明显少于预训练模型。对于五组数据集，K-ADAPTER均优于预训练模型，实现了最先进的性能。

# 背景:

- a.主题和特点:
  - 本文的主题是如何在保持预训练模型原始参数不变的情况下，利用K-ADAPTER来注入多种类型的知识到预训练模型中。
- b.历史发展:
  - 在过去的研究中，注入知识通常直接以某种方式改变了预训练的模型，这不利于接下来的研究。现在，K-ADAPTER 通过注入的adapter 在模型之外添加一些语料库，简化了注入知识的过程并改善了研究效果。
- c.过去方法:
  - 在过去的研究中，预训练模型会被重构以包括新注入的语料库，导致效果不佳和额外的工作量，K-ADAPTER则旨在通过adapter支持不改变预训练模型，使得注意力集中于注入的知识。
- d.过去研究的缺点:
  - 在以前的研究中，注入的知识常常导致训练时的信息丢失，破坏了预先训练模型表达内容特征和效果的基础。

# 方法:

- a.这项研究的理论基础:

  - 本文提出的K-ADAPTER方法建立在RoBERTa模型之上，利用adapter注入额外的先前知识，是一种利于保持原始模型信息的机制。

- b.文章的技术路径（逐步）：

  - 首先，本文使用了一个基于预训练模型的知识特定适配器的方法，用于注入先前的知识体系。其次，本文采用了依赖语法分析的语言知识注入体系。通过这些方法，K-ADAPTER实现了注入多个adapter, 通过K个适配层完成，每个适配层包含两个投影层和n个transformer层，每个transformer层的输入都是上一个adapter层的输出，加上原始预训练模型上层社交网络的输出串联。K-ADAPTER还实现了联通知识空间的操作，以注入不同类型的适配器，从而在知识方面从预训练模型中获得更多的信息和期待的效果。

    # 结论:

- a.本研究的意义:

  - 本文提出了一种K-ADAPTER机制，能很好地将训练效果改善到达最优性能水平，为注入新知识到预训练模型致力有所帮助，在实验中显著地优于以前的模型。

- b.创新、性能和工作量情况:

  - K-ADAPTER通过特定适配器注入知识是十分有创意的, 使得预先训练模型的基础特征不变，同时K-ADAPTER注入的新知识使得模型的性能优于以前的研究方法。该方法训练速度很快，并且训练参数少于以前的研究。

- c.研究结论（列举些点）:

  - 此研究提出的K-ADAPTER注入知识的机制可以在不影响预训练模型的情况下，使模型能够从新知识的注入中更好地表达和理解内容，提高了实体类型、问题解答、关系分类等任务的性能；实验数据表明K-ADAPTER方法与预训练模型相比表现出更好的效果，这证明注入对于模型性能的提升是必要的。 。未来的工作将探索更多类型的知识注入，并将该方法应用到更多的预先训练模型上。

---

本文提出了K-Adapter框架和Adapter结构。Adapter是一种与预训练模型相连的知识模型，被用于注入特定领域上的知识，使得预训练模型可以更好地适应特定领域任务。K-Adapter框架通过保留预训练模型参数的不变性，提供了注入多种知识的可能，并且支持以插件形式添加多个adapter用于分别注入不同领域的知识。 在本文中，通过在 关系分类任务和依赖关系预测任务上预训练 factual adapter and linguistic adapter， 提取事实知识和语言知识。Adapter结构部分，本文采用知识专用适配器架构，与Houlsby等人的添加适配器层的方式不同， 通过 K 个adapter 层将中间层的输出送入 N 层 transformer 计算，并在两个投影层间应用 skip-connection。实验结果表明, K-Adapter可以在多个领域任务上达到优于预训练模型的表现，并且不同adapter之间没有信息流动，可以并行训练，提高了训练的效率和可扩展性。 Pages: [1, 4, 5, 7, 9, 12]