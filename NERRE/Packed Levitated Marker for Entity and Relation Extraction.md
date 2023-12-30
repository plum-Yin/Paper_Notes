# Abstract

最近的实体和关系提取工作主要是研究如何从预先训练的编码器中获得更好的跨度表示（span representation）。然而，现有工作的一个主要限制是他们忽略了跨度（对）之间的相互关系。

在这项工作中，我们提出了一种新的**跨度表示**方法，名为**包装浮动标记**（PL-Marker），通过战略性地包装编码器中的标记，来考虑跨度（对）之间的相互关系。特别是，我们提出了一个**面向邻居**的打包策略，它综合考虑了邻居跨度，以更好地模拟实体边界信息。

此外，对于那些更复杂的跨度对分类任务，我们设计了一个**面向主体**的打包策略，该策略打包每个主体及其所有对象，以模拟<u>同主体跨度对之间的相互关系</u>。

实验结果表明，通过增强的标记特征，我们的模型在6个NER基准测试中领先于基线，并在ACE04和ACE05上获得了4.1%-4.3%的严格关系F1改进，而且速度比以前的最先进模型更快。

---

> "Span representation"（跨度表示）是一种自然语言处理中的技术，用于表示文本中的连续子序列或"span"。这些"span"可能是单词、短语或句子等。将文本表示为"span"可以使得在文本上进行各种任务变得更加容易，例如问答、信息检索和命名实体识别。
>
> 在"span representation"中，每个"span"都被编码为一个向量或张量。这些向量或张量捕捉了"span"中的语义信息，并且可以用作输入到神经网络等机器学习模型中。在某些情况下，"span representation"还可以结合上下文信息，例如在句子中，上下文可以是整个句子。
>
> 总的来说，"span representation"是一种有用的技术，可以帮助自然语言处理模型更好地理解和处理文本数据。

> 在自然语言处理（NLP）中，span pair 是指文本中的两个子串（称为 span），它们在文本中是相互关联的。这两个 span 可能表示一个实体、一个事件或一些其他的语言构成。
>
> 通常情况下，一个 span pair 被表示为一个包含两个 span 的元组，即 (span1, span2)。其中，span1 和 span2 可以是一段文本中的任意子串，它们可以重叠或不重叠。span pair 通常用于建模文本中的关系，如实体关系、语义角色等。
>
> 例如，在实体关系抽取任务中，一个 span pair 可能表示文本中两个实体之间的关系，其中 span1 表示第一个实体的文本，span2 表示第二个实体的文本。在命名实体识别任务中，一个 span pair 可能表示文本中一个实体的起始位置和结束位置。在语义角色标注任务中，一个 span pair 可能表示文本中一个谓词和它的论元。
>
> 因此，span pair 是 NLP 中常用的一种数据表示形式，用于表示文本中不同语言单元之间的关系，可以用于各种文本任务的建模和分析。

---

# Introduction

三种 span representation extraction 方法被广泛应用:

1. T-Concat 将跨度的边界(开始和结束)tokens的表示串联起来以获得跨度表示。它在标记级别收集信息，但忽略了 boundary tokens of a span 之间的连接，当它们通过网络时;
2. Solid Marker 显式地在span前后插入两个solid marker，以突出显示输入文本中的跨度。它插入两对标记来定位span对的主体和对象。但是，该方法不能同时处理多个span pairs，因为它不能从序列中的两个以上的 markers 对中指定一个 span pair 的 solid markers。
3. Levitated Marker 首先设置一对 levitated markers 与 span’s boundary tokens 共享同一位置，然后通过 directional attention 将一对 markers 系在一起。具体来说，一对中的 markers 被设置为在 attention mask matrix 中彼此可见，但对 text markers 和 other pairs of markers不可见。
   1. ignore interrelation between spans (pairs) -> Fig1


现有工作简单地用Levitated Marker取代solid Marker，以实现高效的批处理计算，但牺牲了模型性能。

在这项工作中，我们引入了打包悬浮标记 (PL-Marker)，通过在编码阶段策略性地打包悬浮标记来模拟跨度（对）之间的相互关系。

将悬浮标记打包在一起用于跨度分类任务的一个关键挑战是，插入的悬浮标记数量的增加会以二次方方式加剧 PLM 的复杂性（Ye 等人，2021 年）。

因此，我们必须将跨度分成几组来控制每个输入序列的长度，以获得更高的速度和可行性。在这种情况下，有必要整体考虑**相邻跨度**，这可以帮助模型比较相邻跨度，例如具有相同起始标记的跨度，以获得更精确的实体边界。

因此，我们提出了一种面向邻域的打包策略，将具有<u>相同起始标记的跨度</u>尽可能多地打包到一个训练实例中，以更好地区分实体边界。

---

对于更复杂的跨度对分类任务，理想的打包方案是将所有 span pairs 与 multiple pairs of levitated markers 打包在一起，以对所有跨度对进行整体建模。然而，由于每对悬浮标记已经被 directional attention 绑定，如果我们继续应用directional attention绑定 two pairs of markers，悬浮标记将无法识别其同一跨度的partner marker。

因此，我们采用solid markers和levitated markers的融合，并使用subject-oriented的包装策略，将subject与所有相关对象进行集成建模。具体来说，我们用solid markers强调subject span，并用悬浮标记包装其所有 candidate object spans。此外，我们将object-oriented 的打包策略应用于完整的双向建模(Wu等人，2020)。

我们检查了 PL-Marker 对两个典型跨度（对）分类任务 NER 和端到端 RE 的影响。实验结果表明，在 NER 上，具有面向邻域的打包方案的 PL-Marker 比具有随机打包方案的模型表现得更好，这表明**有必要整体考虑邻居跨度**。并且我们的模型还在六个 NER 基准测试上改进了 T-Concat 模型，这证明了跨度标记获得的特征的有效性。此外，与之前最先进的 RE 模型相比，我们的模型在 ACE04 和 ACE05 上以更高的速度获得了 4.1%-4.3% 的严格关系 F1 改进，并且在 SciERC 上也取得了更好的性能，这表明考虑 **subject-oriented 的 span pairs 之间的相互关系**的重要性。













