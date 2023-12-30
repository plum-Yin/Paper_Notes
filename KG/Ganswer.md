(1)针对RDF Q/A任务，我们提出了两种不同于现有解决方案的图数据驱动框架，将消歧和查询求值结合在一起。在第一个框架中，我们在查询求值时解决了短语连接的歧义;第二种框架解决了短语连接和查询图结构的模糊性问题。图数据驱动框架不仅提高了查询精度，而且大大加快了查询处理速度。

(2)在离线处理中，我们提出了一种图挖掘算法来构建关系提及字典，即将自然语言短语映射到可能的谓词，用于RDF Q/ a中的问题理解。

(3)在在线处理中，为了加快查询评估速度，我们提出了RDF图上匹配q和QU的高效top-k(近似)图匹配算法。

(4)我们在几个真实的RDF数据集(包括QALD基准和WebQuestions基准)上进行了广泛的实验，并将我们的系统与一些最先进的系统进行了比较。我们的方法在QALD基准测试上的性能优于其他系统，而在WebQuestions基准测试上的性能接近最佳

In the running example, we get two semantic relations, i.e., <“directed by”, “film”,“Paul Anderson”> and <“budget of”, “what”,“film”>, as shown in Fig. 2.
They can be combined through the common node phrase “film” as showed in Fig. 2c. In addition, if two node phrases refer to same thing after “coreference resolution” [6], we also combine the corresponding two semantic relations

在运行示例中，我们得到两个语义关系，即 <“directed by”, “film”, “Paul Anderson”> 和 <“budget of”, “what”, “film”>，如图 2 所示. 它们可以通过图 2c 所示的公共节点短语“电影”进行组合。此外，如果两个节点短语在“共指消解”[6]后指代同一事物，我们也将对应的两个语义关系组合起来

2.2.2查询执行如前所述，语义查询图q是N的结构表示。为了回答N，我们需要找到与QS匹配的RDF图G的子图。匹配是根据子图同构定义的(见定义2)。每个子图匹配有一个分数，这个分数来自于每个边和顶点映射的置信度。定义8定义了分数，我们将在后面讨论。我们的目标是找到所有得分在前k的子图匹配。在4.2节中提出了一个最佳优先算法来解决这个问题。
q的每个子图匹配简化了自然语言问题N的一个答案，同时解决了歧义。