# Outline

处理 overlapping community 问题

本文采用 KCPC 模型，研究 DCPC search 问题，旨在找到包含给定查询节点集（a set of query nodes）极大 $k$ 的 KCPC

- 采用 index-based 方法来解决这个问题
- 基于 observation：KCPC 是 maximal cliques 的联合（union）

设计一种新颖紧凑 index（DCPC-Index），以保留 input graph 的 maximal cliques 及其 connectivity information。

- 使用 DCPC-index，查询效率高
- 设计一种 index construction algorithm，并且基于效率和内存消耗来改进此算法。

实验表明，咱的方法 nb

## KCPC

$k$-clique 是具有 $k$ 个节点的完全图。

如果两个 $k$-cliques 共享 $k-1$ 个节点，则它们被认为是相邻的。 

KCPC 被定义为可以通过一系列相邻的 $k$-cliques 相互到达的 $k$-cliques 的最大联合

使用 $k$ 值来表示KCPC的连接密度，并将 **DCPC 问题**视为**包含查询节点的 $k$ 值极大的KCPC**

## Issues need to be solved.

- 设计一个紧凑的 index 结构来表示 input graph 中所有可能 k 值的 KCPC
- 通过 index 有效回答 DCPC query
- 有效构造 index

## Methods

我们将 maximal cliques 作为 KCPC 的构建块，并提出一种名为 clique adjacency tree ($\mathbf{T}_\phi$) 的树结构，它可以保留 maximal clique 的 clique connectivity information.

基于 $\mathbf{T}_\phi$，我们可以很容易地获得 graph $G$ 中特定 $k$ 的 KCPC。然而，$\mathbf{T}_\phi$ 无法有效处理 DCPC 查询.

为了提高查询处理的效率，我们基于 $\mathbf{T}_\phi$ 进一步开发了一种新树结构，名为 ordered adjacency tree ($\mathbf{T}_o$).

使用 $\mathbf{T}_o$，我们可以通过仅探索，与查询节点相关的 subtrees，来有效地回答 DCPC query.

# Definition&Lemma

## Clique

### Clique $C$

给定图 $G$，$G$ 中的 clique $C$ 是一组节点，使得 $C$ 导出子图是一个完全图，即 $\forall u,v\in C\Rightarrow (u,v)\in E(G)$。

如果 G 中不存在 $C'$，满足 $C\subset C'$，那么 clique $C$ 被称为 **maximal clique**

### k-clique

给定一个$G$ 和一个整数 $k$，一个 $k$-clique $C_k$ 是 $G$ 中的一个 clique，并且 $C_k$ 有 $k$ 个节点.

为了清楚起见，我们使用不同的上标，例如 $C^1_k$ 和 $C^2_k$，来区分具有相同 $k$ 值的，不同具体 $k$-cliques

### k-clique Adjacency

给定$G$中两个 $k$-cliques $C^1_k$ 和 $C^2_k$，如果 $C^1_k$ 和 $C^2_k$ 共享 $k-1$ 个节点，即 $|C^1_k\cap C^2_k|=k-1$

### k-clique Connectivity

给定 $G$ 中的两个 $k$-cliques $C_k^s$ 和 $C_k^t$， $C_k^s$ 和 $C_k^t$ 是 $k$-clique connected，如果 $G$ 中存在一系列 $k$-cliques，$C_k^1,...,C_k^n$，使得 $C_k^1=C_k^s, C_k^n=C_k^t$，并且对于 $1 \leq i< n$，其中 $C^i_k$ 和 $C^{i+1}_k$ 是 $k$-clique adjacent.

$C_k^s$ 和 $C_k^t$ 可以通过一系列 adjacent $k$-clique 连接起来。

### KCPC $\mathcal{C}_k$

给定一个 $G$ 和一个整数 $k$，一个 KCPC $\mathcal{C}_k$ 是 ${k}$-cliques 的最大并集，使得 $\mathcal{C}_k$ 中任意两个 $k$-cliques 是 $k$-clique connected.

我们还使用不同的上标，例如 $\mathcal{C}_k^1$ 和 $\mathcal{C}_k^2$ 来区分具有相同 $k$ 值，但不同的 KCPC。

## Maximal Clique

### Lemma 4.1

给定一个 G 和一个整数 k，

对于 G 中的任何 maximal clique $C$，且 $|C| > k$，C 中任意两个 k-clique 都是 k-clique connected

根据 Lemma 4.1，任何 maximal clique C 是 KCPC 的子集,其中 $|C|>k$

解释：因为KCPC是 k-clique 的集合，节点数大于 k 的 maximal clique 是 k-clique connected. KCPC 包括了附近所有的 k-clique。

因此，**KCPC 可以基于maximal cliques 计算**。

### k-maximal-clique Adjacency

给定 $G$ 中的两个 maximal-cliques $C^1$ 和 $C^2$ 以及一个整数 $k$，如果 $C^1$ 和 $C^2$ 共享 $k - 1$ 个节点，即 $|C^1\cap C^2| = k - 1$，那么它们两就是。

### k-maximal-clique Connectivity

给定 $G$ 中的两个maximal cliques $C^s$ 和 $C^t$ 以及一个整数 $k$，

如果 $G$ 中存在一系列 maximal cliques $C^1,…,C^n$ 则 $C^s$ 和 $C^t$ 是 k-maximal-clique connected，

且有 $C^1=C^s, C^n=C^t$，并且对于 $1 \le i \le n$，$C^i$ 和 $C^{i+1}$ 是 $k$-maximal-clique adjacent

### Lemma 4.2

给定 $G$ 中的 KCPC $\mathcal{C}_k$，$\mathcal{C}_k$ 是 maximal cliques 的极大并集，

使得 $\mathcal{C}_k$ 中的任何两个 maximal cliques，都是 $k$-maximal-clique connected.

根据 Lemma 4.2，G 中具有不同 $k$ 值的 KCPC 由 maximal cliques组成。

因此，我们以 maximal cliques 而不是 k-clique 为基础来计算 G 中的 KCPC。

接下来的问题是如何组织这些 maximal cliques，以针对特定 $k$ 有效地计算 KCPC。

## Clique Adjacency Graph

### clique adjacency number

给定两个 maximal cliques $C^1$ 和 $C^2$，$C^1$ 和 $C^2$ 的 clique adjacency number，记为 $\phi(C^1,C^2)$，定义为$C^1$ 和 $C^2$共享的节点数，即 $\phi(C^1,C^2)=|C^1\cap C^2|$.

### clique adjacency graph $\mathbf{G}_\phi$

给定图 $G$，$G$ 的 clique adjacency graph 标记为 $\mathbf{G}_\phi= (V,E,W)$，

$\mathbf{G}_\phi$ 是一个加权无向图，使得 <u>**$\mathbf{G}_\phi$ 中的每个节点 $v$，代表 $G$ 中的 maximal clique $C$**</u>，及其权重 $w(v)=|C|-1$，

如果 $\mathbf{G}_\phi$ 中 maximal cliques $C_1$ 和 $C_2$ 相邻，则在 $\mathbf{G}_\phi$ 中相应的节点 $v_1$ 和 $v_2$ 之间有一条边 $e$，

- $e$ 的权值为 $C_1$ 和 $C_2$ 的 clique adjacency number，即 $w(e)=\phi(C ^1,C^2)$

### Path weight $w(P_{u,v})$

在一个加权图中，给定路径 $P_{u,v}=\{u,..,v\}$，路径的权重表示为：$w(P_{u,v})$  定义为 $P_{u,v}$ 中的最小边权重，

- 即 $w(P_{u,v})=\min\{w{\large(}(u',v'){\large)}|(u',v')\in P_{u,v}\}$

### $k$-weighted connected component $CC_k$

给定一个加权无向图 $G$ 和一个整数 $k$，$k$-weighted connected component，表示为 $CC_k$，是 **$G$ 中节点的极大子集**，

其中，任意两个节点 $u$ 和 $v$ 之间的路径 $P_{u, v}$，有 $w(P_{u,v})\ge k-1$

请注意，对于极大子集仅具有孤立节点 $v$ 的极端情况，使得 $w(v)\ge k-1$，我们仍然认为它是一个 k-weighted connected component

### Lemma 4.3

给定图 $G$ 及其对应的 $\mathbf{G}_\phi$，对于给定的整数 $k$，$\mathbf{G}_\phi$中 $CC_k$ 表示 $G$ 中唯一的 KCPC.

如 Lemma 4.3 所示，我们可以通过检索对应 $\mathbf{G}_\phi$ 的 $CC_k$，来计算图 $G$ 中的 KCPC。

然而，$\mathbf{G}_\phi$ 在计算 $CC_k$ 方面仍然包含大量冗余信息。

例如 Fig.3a 中的 edge $(v_4,v_6)$ 对于计算 4-weighted connected component 是多余的。因此，对于计算 4-CPC 是多余的。

在展示如何减少 $\mathbf{G}_\phi$ 中的冗余之前，我们首先展示以下 Lemma

### Lemma 4.4

给定图 $G$ 及其对应的 $\mathbf{G}_\phi$，令 $v_1$ 和 $v_2$ 为 $\mathbf{G}_\phi$ 中的两个节点，$C_1$ 和 $C_2$ 为 $G$ 中对应的极大团，

$\mathcal{P}_{v_1,v_2}$ 为 $\mathbf{G}_\phi$ 中 $v_1$ 和 $v_2$ 之间所有路径的集合，

- 则 $C_1$ 和 $C_2$ 是 k-maximal-clique connected，其中 $k=\max\{w({P}_{v_1,v_2})|{P}_{v_1,v_2}\in\mathcal{P}_{v_1,v_2}\}$，权重最大的路径

根据 Lemma 4.4，对于$G$中的任意两个 maximal cliques $C^1$和$C^2$，

- $C^1$ 和 $C^2$ 是 $k$-maximal-clique connected，
- 最大的 $k$ 值由$\mathbf{G}_\phi$中$v_1$和$v_2$之间**权重最大**的路径决定。

并且根据 k-maximal-clique Connectivity 的定义，

- 如果 $C^1$ 和 $C^2$ 是 $k$-maximal-clique connected，
- 可以推导出 $C^1$和$C^2$，对于 $\forall k'< k$，满足 $k'-$maximal-clique connected。

换句话说，对于$C^1$和$C^2$，$\mathbf{G}_\phi$ 中 $v_1$ 和$v_2$ 之间**权重最大**的路径，足以维持$C^1$和$C^2$的 maximal-clique connectivity information

## Clique Adjacency Tree

### Clique Adjacency Tree $\mathbf{T}_\phi$

给定 $\mathbf{G}_\phi$ 的 clique adjacency tree，记为 $\mathbf{T}_\phi$，是 $\mathbf{G}_\phi$ 的**总权重极大的生成树**（<u>对于构建这种树我不理解</u>）

根据前两个 definitions，显然对于图 $G$，其 $\mathbf{G}_\phi$ 和 $\mathbf{T}_\phi$ 中的 $CC_k$ 相同。

然而，$\mathbf{T}_\phi$ 只解决了计算特定 k 的 KCPC 的问题。

下面的问题是，给定查询 q 的 DCPC 的 k 值是多少？直接用 $\mathbf{T}_\phi$ 很难回答这个问题

为了解决这个问题，我们提出了基于 $\mathbf{T}_\phi$ 的 ordered adjacency tree $\mathbf{T}_o$

$\mathbf{T}_o$ 不仅像 $\mathbf{T}_\phi$ 那样维护 KCPC 信息，而且还可以用于计算给定查询 $q$ 的 DCPC

### ordered adjacency tree $\mathbf{T}_o$

给定一个 $\mathbf{T}_\phi$，$\mathbf{T}_\phi$ 的 $\mathbf{T}_o$ 递归定义如下：

- 如果 $\mathrm{T}_\phi$ 只有一个节点，则 $\mathrm {T}_o$ 有一个节点 $\mathrm{T}_\phi$.
- 否则，设 $e_{\min}$ 为 $\mathrm{T}_\phi$ 中**权重最小的边**（任意打破任何关系）.
- $\mathrm{T}_o$ 的根节点 $t$ 表示 $e_{\min }$，通过从 $\mathrm{T}_\phi$ 中移除 $e_{\min }$，我们有关于$\mathrm{T}_\phi$的两个相连的子树 $\mathrm{T}_\phi^1$ 和 $\mathrm{T}_\phi^2$.
- 令 $\mathrm{T}_o^1$ 和 $\mathrm{T}_o^2$ 为 $\mathrm{T}_\phi^1$ 和 $\mathrm{T}_\phi^2$ 对应的有序邻接树，并设 $t_1$ 和 $t_2$ 分别为 $\mathrm{T}_o^1$ 和 $\mathrm{T}_o^2$ 的根节点.
- 然后我们通过添加两条边 $\left(t, t_1\right)$ 和 $\left(t, t_2\right)$ 来合并 $\mathrm{T}_o^1$ 和 $\mathrm{T}_o^2$ 进入 $\mathrm{T}_o$ 并获得 $\mathrm{T}_o$.

在 $\mathbf{T}_\phi$ 的 $\mathbf{T}_o$中，

- 每个 leaf 节点代表 $\mathbf{T}_\phi$ 中的一个 vertex，其权重为其 $\mathbf{T}_\phi$ 中对应的 vertex 权重
- 每个 internal 节点代表 $\mathbf{T}_\phi$ 中的一条 edge，其权重为其 $\mathbf{T}_\phi$ 中对应的 edge 权重。

我们有以下引理

### Lemma 4.5

给定一棵 $\mathbf{T}_o$，对于任意树节点 $t$，令 $k$ 为 $t$ 的权值，

- 则以 $t$ 为根的子树的 leaf 节点，是 $\mathbf{G}_\phi$ 中唯一 $CC_k$ 的子集

## DCPC-index

对于给定的图 $G$，DCPC-Index 包含三个组件： 
- Ordered Adjacency Tree ($\mathbf{T}_o$)：$G$ 的有序邻接树。 
- Maximal Cliques ($\mathbf{MC}$)：$G$ 中 maximal cliques，每个 maximal clique 存储它的节点。
- Inverted Maximal Clique Index (IMCI)：从节点到 maximal cliques 的映射，每个节点存储它所在的 maximal cliques。

### Index-based Query Processing

根据 Lemma 4.5，对于 $\mathbf{T}_o$ 中权重为 k 的树节点 t，

- 以 $t$ 为根的子树的 leaf 节点 是唯一的 $CC_k$ 的子集，
- 这意味着由这些 leaf 节点表示的相应极大团的并集 是 $G$ 中 KCPC 的子集。

因此，要找到查询 q 的 DCPC，它等于找到树节点 $t$，

- 使得以 $t$ 为根的子树的 leaf 节点表示的相应 maximal cliques 的并集 是 $q$ 的 DCPC

为了在 $\mathbf{T}_o$ 中找到这样的树节点，我们首先将其形式化如下

## Tree Node

### Tree Node Subgraph

树节点子图

给定图 G 及其 ordered adjacency tree $\mathbf{T}_o$，对于树节点 t，

- 其 tree node subgraph 是由以 t 为根的，
- subtree 的 leaf 节点表示的，
- 相应 maximal cliques 的并集。

树节点子图是由以t为根的子树的 所有叶子节点 所表示的最大团的并集

### DCPC Tree Node $t_q$

给定图 $G$ 及其 $\mathbf{T}_o$，

- 对于查询 $q$，其 DCPC 树节点（记为 $t_q$）是 $\mathbf{T}_o$ 中的树节点，
- 因此其树节点子图是 $q$ 的 DCPC。

### Lemma 4.6

给定图 $G$ 及其 $\mathbf{T}_o$，对于给定的查询 $q$，其 $t_q$ 是 $\mathbf{T}_o$ 中的树节点 $t$

满足以下条件：

1. $q$ 中的节点，包含在 $t$ 的 tree node subgraph 中
2. $t$ 的 parent 权重小于 $t$
3. 在满足条件 (1) 和 (2) 的树节点中，$t$ 的权重最大。

---

根据上一个 definition，我们可以通过在 $\mathbf{T}_o$ 中找到 $q$ 的 DCPC 树节点 $t_q$，来回答对 $q$ 的 DCPC 查询。

由于 $t_q$ 是其 tree node subgraph 中包含 $q$ 中所有节点的，最大权值的树节点。

可以先找到其tree node subgraph中，包含$q$中节点的所有树节点，作为candidate树节点，然后选择candidate树节点中，最大权重的树节点，作为 $q$ 的DCPC 树节点。

按照这个想法，Algorithm 2 中显示了一个简单的实现，DCPC-Index-Query。为了清楚起见，我们将 $q$ 或 $G$ 中的节点称为节点，将 $\mathbf{T}_o$ 中的节点称为树节点。

# DCPC-Index-Query*(Query $q$, DCPC-Index $\mathcal{I}$)

Algorithm 2 必须为至少包含 $q$ 中一个节点的 maximal cliques 的所有可能组合，计算共同祖先。

当 $q$ 中的节点数很大时不适用。为了解决这个问题，我们进一步提出了 DCPC-Index-Query* 算法。

如果每个可能的组合的计算都可以共享，那么只需要遍历一次 $\mathbf{T}_o$ 就可以得到所有的候选树节点。根据 Lemma 4.6，候选树节点的树节点子图包含 q 中的所有节点。换句话说，如果我们能够确定 t 的树节点子图是否包含 q 中的所有节点，就足以识别候选树节点$t$. 

为了实现这一目标，我们在查询处理过程中为每个树节点引入一个 bitmap 来指示它包含的 q 中的节点，我们可以通过检查对应的 bit 来确定树节点的树节点子图是否包含 q 中的特定节点 bitmap

---

DCPC-Index-Query* 以自下而上的方式搜索树。它使用 priority query $Q$，来存储需要访问的树节点（第 2 行）。

Note: 树节点推入 $Q$ 的<u>优先级</u>是基于它在 $\mathbf{T}_o$ 中的级别，而不是它的权重。 

对于 $Q$ 中的树节点 $t$，DCPC-Index-Query 使用 bitmap 来**指示** $t$ 的树节点子图中包含的 $q$ 中的**节点**（第 2 行）。 

DCPC-Index-Query 首先依次调用 `initDCPC`、`computeDCPC` 计算 $q$ 的DCPC 树节点 $t_q$（第3-4行），然后调用 `retrieveDCPC` 计算 $q$ 的 DCPC（第5行）


## `initDCPC`

识别包含 $q$ 中至少一个节点的 maximal cliques，并通过将代表这些maximal cliques 的相应树节点推入优先队列来初始化搜索过程.

对于 $q$ 中的每个节点 $v_i$

- 它首先通过 `DCPC-Index` 的 IMCI 找到包含 $v_i$ 的maximal cliques（第 7-8 行）。
- 然后，对于每个 maximal clique $C$，它迭代 $q$ 中的每个节点 $v_j$，并设置 bitmap 中的相应 bit，
- 对于表示 $C$ 的树节点 $t$ 以指示 $v_j$ 是否包含在 $C$ 中（第 10-11 行）。
- 最后，如果 $t$ 还没有被 push into $Q$，`initDCPC` 将 push $t$ into $Q$（第 12 行）

## `computeDCPC`

为查询 $q$ 计算 DCPC 树节点 $t_q$

- 直观地，它首先计算满足Lemma 4.6 的条件（1）和（2）的candidate 树节点（第 15-22 行）。
- 然后，它根据 Lemma 4.6 的条件（3）确定具有最大权重的树节点，即 $q$ 的 $t_q$（第 23 行）。
- 具体来说，`computeDCPC` 使用 $\mathcal{S}$ 来存储满足条件（1）和（2）的candidate树节点（第 14 行）。
- 为了找到满足条件（1）的树节点，`computeDCPC`通过 priority queue $Q$ 自底向上搜索 $\mathbf{T}_o$.
- 在搜索过程中，在 $t$ 的 bitmap 中设置的bit，也在其parent树节点 $t_p$ 的bitmap中设置，这表示 $t_p$ 的tree node subgraph包含 $q$ 中，$t$ 的tree node subgraph包含的，所有节点（第 20 行）。
- 当找到满足条件 (1) 的树节点 $t$ 时，如果 $t$ 也满足条件 (2)，则 `computeDCPC` 将 $t$ 插入 $\mathcal{S}$（第 17 行）。
- 最后，`computeDCPC` 返回 $\mathcal{S}$ 中权重最大的树节点（第 23 行）

## `retrieveDCPC`

计算给定树节点 $t$ 的tree node subgraph。

它首先使用stack $\mathcal{S}$ 以 DFS 的方式遍历树，并计算以 $t$ 为根的子树的 leaf 节点（第 21-27 行）。所有 leaf 节点都存储在 $\mathcal{S}$ 中（第 25 行）。

获得 leaf 节点后，`retrieveDCPC` 通过 MC（第 28-29 行）计算这些 leaf 节点表示的 maximal cliques 的并集 $C$，
并在第 30 行返回 $C$。

19. Procedure `retrieveDCPC`(TreeNode $t$)
20. Stack $S\leftarrow\empty$; Set $\mathcal{S}\leftarrow\empty$; Set $\mathcal{C}\leftarrow\empty$;
21. $S$.push($t$);
22. while $S\not=\empty$ do
23.   $t \leftarrow S.\text{pop}()$;
24.   if $t$ is a leaf node then
25. ​    $\mathcal{S}.\text{insert}(t)$; 
26.   else
27. ​    $S$.push($t$.left); $S$.push($t$.right);
28. for each $t \in \mathcal{S}$ do
29.   $\mathcal{C} = \mathcal{C}\cup \mathcal{I}.MC[t’]$; // $t’$ is the maximal clique represented by $t$
30. return $\mathcal{C}$

# DCPC-Index-Cons*(Graph G) 





# Conclusion and Future Work

群落搜索在图分析中很重要，并可用于许多实际应用中。在本文中，我们研究了最密集的悬崖渗水社区搜索问题。我们设计了一个紧凑的索引DCPC-Index并提出了基于DCPC-Index的DCPC-Index-Query*算法。

我们还展示了一种基于DCPC-Index定义的索引构建算法，DCPC-Index-Cons，并在效率和内存消耗方面进一步改进了该算法。实验结果证明了我们提出的算法的效率。未来工作的一个有趣的方向是，当图被动态更新时，比如边的插入/删除，如何有效地维护DCPC-Index。

由于受插入/删除的边影响的最大剪子可以在由与该边相关的节点及其邻居所引起的子图中得到约束，我们可以 我们可以很容易地维护MC和IMCI。然而，当图被更新时，要有效地维护To是不难的。另一个有趣的方向是如何推广我们的索引以支持文献中提出的其他重叠社区模型的社区搜索问题。在未来，我们计划探索新的技术来解决这两个问题。
