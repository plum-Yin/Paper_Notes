# Abstract

最近有些关于在复杂网络中的 overlapping community structure 的研究，但是这些算法有三个缺点：

1. 算法使用相同的全局标准来检测 subgraph 是否有资格作为community，即社区筛选的标准是预先确定好的。
2. 对于整个 network 搜寻 communities 通常没有意义，并且耗时耗力。
3. 通常算法不支持 dynamically evolving networks。

本文专注于 overlapping community 的 online search，即 ==给定一个查询顶点==，以在线的方式该顶点所属的有意义的 overlapping community。好处是：对于每次查询，都能量身定制合适的社区选择标准。

文章提出了几种 online overlapping community search 算法和参数选择的理论指导，有实验证明模型和算法的有效性，还建议了一些相关的潜在应用。

# Definition

The **density** of a community: average degree.

**Community**: 

- $k$-clique (complete graph with k vertices)
- is defined as a connected component called a <u>k-clique component</u>.
- larger $k \Rightarrow$ denser density

two k-cliques are **adjacent** if they share $k − 1$ vertex in $G$

clique adjacency: Two $k$-cliques are adjacent if they share $k − 1$ vertices

==$k$-clique component==

- 令 $C$ 为 $k$-clique 图中的 connected component
- $k$-clique component 是由 $C$ 中的节点，表示所有 $k$-cliques 的联合。

# OCS

## OCD

- overlapping community detection
- input: $k$
- 检测出所有不小于相同阈值的 community
- 因为需要提前计算好所有的 communities，所以在实际操作中下不现实。具体有几个原因
  - 耗时耗力
  - 全局标准不好
  - 动态进化图不太支持

## OCS

- overlapping community search
- Input: a vertex and a specific $k$
- 在线方法检测出包含此 vertex 的重叠社区。

文章主要在研究 OCS

# $(\alpha,\gamma)$-OCS

$\alpha$-adjacency

- Graph $G(V, E)$ 的两个子图 $G_1$ 和 $G_2$ 至少共享 $\alpha$ 个顶点

$\gamma$-quasi-$k$-clique

- a $k$-node subgraph of $G$ with at least $\lfloor \gamma \frac{k(k−1)}{2}\rfloor$ edges (where $0 \le \gamma \le 1$)

Problem:

- 给定Graph $G$，一个查询节点 $v_0$，一个正整数 $k$，
- $(\alpha,\gamma)$-OCS problem：发现所有包含 $v_0$ 的 $\gamma$-quasi-$k$-clique

## Density

对于 $\gamma$-quasi-$k$-clique，入射到任意一组 $x$ 个顶点的**边数**至少为 $\max\{0, \gamma\binom{k}{2} −\binom{k-x}{2}\}$。

## Lower bound on average degree


$(\alpha, \gamma)$-OCS 找到的每个社区的 average degree 有一个下界

$$
2\max\{0,\min\{f(1),f(\alpha)\}\}
$$

where $f(x)=\frac{\gamma\binom{k}{2}-\binom{k-x}2}{x}$

## Consistency

在 $(\alpha,\gamma)$-OCS 中，如果 $C$ 是包含查询顶点 $v_0$ 的 community，则对于任何其他顶点 $v \in C$ 作为查询顶点，$C$ 也作为其 community 返回。

## Parameter selections

### $\gamma$ & $k$

$\gamma$ should vary with $k$.

为了避免极端情况，即一个孤立点和剩余 $k-1$ 组成clique，需要有
$$
\gamma\ge\frac{k-2}{k}+\frac{2}{k(k-1)}\quad\text{Eq. 1}
$$
当 $k$ 固定时，$Pr(Y = \gamma)$ 在 $\gamma$ 接近平均密度时达到最大值。

一般来说，有意义的社区应该具有比平均密度大得多的密度。因此，我们应该选择足够大的密度，以避开那些密度平均的琐碎社区。

### $\alpha$

为了避免两种情况

1. 当 $\gamma$-quasi-$k$-clique 的 density 主要由其 $α$-size subset 贡献时，会发生第一个微不足道的情况。导致与subset组合的 clique 之外的任何其他 $k − \alpha$ 个顶点，将是有效的相邻 clique。这个微不足道的案例显然会导致毫无意义的社区
2. 当两个 $\gamma$-quasi-$k$-cliques 相交于一个非常稀疏的 $α$-size 子集时。在实际网络中，如果两个子图的公共部分过于稀疏，则很难将它们视为一个紧密相连的社区。如果所有边都位于其余 $k − \alpha$ 个顶点之间，就会发生这种情况

组合两种情况，需要限制 $\alpha$ 的取值范围为：
$$
\lfloor k+1/2-\sqrt{\gamma k^2-\gamma k +1/4}
\rfloor
\le \alpha\le
\lceil \sqrt{1/4+k(k-1)\gamma}-1/2
\rceil
\quad\text{Combined Eq. 4\&5}
$$

---

Let $\alpha = k − \Delta$，$\Delta$ 是小于 $k$ 的正整数. 

Only when $∆ = 1$, $α$ satisfies Eq. 4 and Eq. 5,
$$
\forall k\ge2,\;\frac{k-2}{k}+\frac{2}{k(k-1)}\le\gamma\le1.
$$

# Algorithm

## Improved OCS

Input: $G(V, E)$, $v_0$, $\alpha$, $\gamma$, $k$; 

Output: The overlapping communities containing $v_0$

1. $\mathcal{R} \leftarrow \empty$; 
2. while $C \leftarrow \text{next\_clique}(v_0), C\ne \empty$ do 
3. ​          $C \leftarrow \text{expand}(C)$;   Find the clique component of $C$
4. ​          $\mathcal{R} \leftarrow \mathcal{R} \cup \{\mathbf{C}\}$; 
5. end while 
6. Return $\mathcal{R}$

## $\text{next\_clique}(v_0)$

enumerate each unvisited $γ$-quasi-$k$-clique containing vertex $v_0$

通过 $v_0$ 获得 clique

DFS procedure of $\text{next\_clique}(v_0)$

1. $U \leftarrow \{{v_0}\}$; 
2. $\text{DFS}(U, v_0); $
3. **procedure** $\text{DFS}(U,u) $
4. ​       **if** $|U| = k$ **then** 
5. ​             **if** $U$ is a $\gamma$-quasi-$k$-clique **And** $U$ is unvisited **then** 
6. ​                   **Return** $U$; 
7. ​             **else** 
8. ​                   **Return**; 
9. ​             **end if** 
10. ​       **end if**
11. ​       **if** $g(U) < \gamma\frac{k(k-1)}{2}$ **then Return**; 如果不满足条件，不算是 clique
12. ​       end if
13. ​       **for all** $(u, v) \in E, v\not\in U$ **do**  ==主要的代码在这==
14. ​              $\text{DFS}(U \cup \{v\}, v)$; 
15. ​       end for
16. end procedure

## $\text{expand}(C)$

finding the clique component containing $C$

通过 clique $C$ 获得 community

- Input: $A$ $\gamma$-quasi-$k$-clique $C$; 
- Output: The clique component of $C$ 

1. $A \leftarrow C$; 
2. $\text{Expand\_clique}(C)$; 
3. **return** $A$; 
4. **procedure** $\text{Expand\_clique}(C)$
5. ​     for all $S_1⊂ C$, $|S_1| ≤ k − α$ do 
6. ​          if $g(C − S_1) < \gamma \frac{k(k−1)}{2}$ then Continue 
7. ​          end if
8. ​          for all $S_2\in \text{Candidate}(C − S_1)$, $|S_1| = |S_2|$ do 
9. ​               $C^′\leftarrow (C − S_1) \cup S_2$; 
10. ​               if $C^′$ is unvisited And $C^′$ is a $γ$-quasi-$k$-clique then  ==approximate method: add $S_1-A\not=\empty$==
11. ​                     $A \leftarrow A \cup S_2$; 
12. ​                     $\text{Expand\_Clique}(C^′)$; 
13. ​               end if 
14. ​         end for 
15. ​    end for 
16. end procedure

$\text{Candidate}(C − S_1)$

- 用于寻找与 $|S_1|$ 大小相同的，来自 $C − S_1$ 的 local community 的子集 $S_2$ 

## Duplication detection

`next_clique()` 和 `expand()` 中可能会出现，相同的 clique 但是不同的 paths，例如：a-b-c 和 b-a-c。论文中使用 hash table 存储 visited cliques 解决这个问题。

## Approximate Algorithm

`next_clique()`

- 由于 `next_clique()` 可能会枚举指数数量级别的 $γ$-quasi-$k$-cliques
- 为了减少枚举空间，我们只枚举一个未访问的clique，它包含至少一个不属于任何已发现communities的新顶点
- 可以通过稍微改变 DFS 过程来实现这种启发式算法。令 $v_0, v_1, ..., v_k$ 为 DFS 树中的顶点序列（从根 $v_0$ 开始）。我们要求第二个顶点 $v_1$ 是一个新顶点。这样，我们确保新 clique 至少包含一个新顶点。请注意，我们强制顶点序列尽可能早地包含一个新顶点，并且 $v_1$ 是最早添加到序列中的顶点。因此，$v_1$ 必须是一个新的顶点。

`expand()`

- 采用启发式的方法去发现一个 short clique sequence（DFS order）
- $C_i$ 和 $C_{i+1}$ 是两个连续的 visited cliques，并且 adjacent，我们通过 $C_{i+1}-\cup_{j\le i}\not=\empty$
- 令 $C$ 为 $(\alpha,\gamma)$-OCS 中的一个 community，有 shortest clique sequence $C_1,C_2,...,C_m$，
  - $C_i$ 至少与其之前的一个 clique 相邻，有$\cup_{1\le i\le m}C_i=C$
  - 即，$C_{i+1}$ 至少包含一个之前已经 visited 顶点

**近似算法不仅速度快，准确率也不错。**

# Influence of $k$

# Influence of $\alpha$ and $\gamma$



# Conclusion

大多数真实的网络都有重叠的社区结构。在本文中，我们提出了一个新的重叠性感知的社区搜索问题：重叠社区搜索。与重叠社区检测相比，我们的模型要轻得多，并支持在线查询回答。我们揭示了该模型背后的原理，并为调整该模型提供了理论指导。

我们设计了几种精确算法和一种有效的近似算法来寻找有意义的重叠社区。我们进行了广泛的实验，表明精确算法和近似算法都能有效地发现真实网络中的有意义的重叠社区，而且近似解决方案相当高效，支持百万节点图的在线（几毫秒内）查询回答。
