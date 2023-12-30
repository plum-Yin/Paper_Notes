# Overview



 图2概述了我们的工作。首先，我们从ShareGPT.com(一个用户可以分享他们的ChatGPT对话的网站)上收集了大约70K个对话。接下来，我们增强了由Alpaca提供的训练脚本，以更好地处理多轮对话和长序列。培训是在一天内用PyTorch FSDP在8个A100 gpu上完成的。

为了服务演示，我们实现了一个轻量级的分布式服务系统。我们通过创建一组80个不同的问题并使用GPT-4来判断模型输出，对模型质量进行了初步评估。为了比较两个不同的模型，我们将每个模型的输出组合为每个问题的单个提示。然后将提示发送到GPT-4, GPT-4评估哪个模型提供更好的响应。下面的表1显示了美洲驼、羊驼、美洲驼和骆马的详细比较。

# Training

Vicuna是通过微调LLaMA基础模型创建的，该模型使用了从ShareGPT.com收集的大约70K用户共享对话和公共api。为了确保数据质量，我们将HTML转换回markdown并过滤掉一些不合适或低质量的样本。此外，我们**将冗长的对话划分为更小的片段，以适应模型的最大上下文长度**。



# Evaluate

While this proposed evaluation framework demonstrates the potential for assessing chatbots, it is not yet a rigorous or mature approach, as large language models are prone to hallucinate. Developing a comprehensive, standardized evaluation system for chatbots remains an open question requiring further research.





