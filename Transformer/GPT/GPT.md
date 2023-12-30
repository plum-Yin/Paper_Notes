# Feature-based & Fine-tuning

Feature-based和fine-tuning都是指在使用机器学习模型进行迁移学习时的两种常用方法，它们的主要区别在于模型的训练方式和目的。

Feature-based方法是指利用预训练模型的特征提取能力，将其作为新任务模型的输入特征，然后在新任务上训练一个新的分类器。具体地，通常会先用一个大规模的数据集（如ImageNet）对模型进行预训练，然后去掉模型的分类层，将预训练模型的输出作为输入特征，再添加一个新的分类层，使用新任务的数据对新的分类层进行训练。这种方法在新任务的数据量较小时尤其有用，可以大大减少模型需要的训练数据量，从而提高模型的泛化能力。

Fine-tuning方法是指在预训练模型的基础上，针对新任务对整个模型进行微调，包括原有的参数和新添加的参数。具体地，首先使用预训练模型在新任务的数据集上进行初始化，然后通过反向传播算法进行训练，以更新模型的所有参数。这种方法的目的是在预训练模型的基础上，让模型更好地适应新任务的特点和数据分布，从而提高模型的性能。

因此，feature-based方法更强调利用预训练模型的特征提取能力，通过添加新的分类器实现迁移学习；而fine-tuning方法则更强调在预训练模型的基础上微调模型，以适应新任务的数据和特征分布。两种方法的选择取决于实际应用场景和数据集的特点。

---

虽然gpt在nlp下游任务中表现的情况没有bert模型好，但是在文本生成任务上表现出色。

# Abtract

我们证明，通过在不同的 unlabled 文本语料库上对语言模型进行generative pre-training，然后对每个特定任务进行判别式fine-tuning，可以实现这些任务的巨大收益。与以前的方法相比，我们在fine-tuning期间使用任务感知输入转换来实现有效传输，同时需要对模型架构进行最少的更改。我们证明了我们的方法在广泛的自然语言理解基准上的有效性。我们的通用任务不可知模型优于使用为每项任务专门设计的架构的有区别训练的模型，显着改进了所研究的 12 项任务中的 9 项的最新技术水平。



Transformer的Decode部分对mask是防止看到未来的信息






