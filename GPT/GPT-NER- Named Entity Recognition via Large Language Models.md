# Abstract

尽管大规模语言模型(LLM)在各种NLP任务上取得了SOTA性能，但其在NER上的性能仍然明显低于监督基线。这是由于NER和LLMs这两个任务之间的差距:前者本质上是序列标记任务，而后者是文本生成模型。

本文提出GPT-NER来解决这个问题。GPT-NER通过将序列标记任务转换为易于LLMs适应的生成任务来弥合这一差距。例如，在输入文本中查找位置实体的任务`Columbus is a city`被转换为生成文本序列 `@@Columbus## is a city`，其中特殊标记`@@##` 标记要提取的实体。为了有效解决llm的幻觉问题，即llm有过度自信地将 NULL 输入标记为实体的强烈倾向，本文提出一种**自我验证策略**，促使llm自问所提取的实体是否属于已标记的实体标签。

在五个广泛采用的NER数据集上进行了实验，GPT-NER取得了与全监督基线相当的性能，这是第一次。更重要的是，GPT-NER在低资源和少样本的设置中表现出更大的能力，当训练数据量极其稀缺时，GPT-NER的表现明显优于监督模型。这展示了GPT-NER在已标记样本数量有限的真实NER应用程序中的能力。