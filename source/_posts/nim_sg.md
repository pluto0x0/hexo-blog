---
title: 博弈论和SG函数基础
tags: [博弈论]
categories: 数学
date: 2021-08-17
---

## 公平组合游戏 

>公平组合游戏[^gpzh]的定义如下：
>
> - 游戏有两个人参与，二者轮流做出决策，双方均知道游戏的完整信息；
> - 任意一个游戏者在某一确定状态可以作出的决策集合只与当前的状态有关，而与游戏者无关；
> - 游戏中的同一个状态不可能多次抵达，游戏以玩家无法行动为结束，且游戏一定会在有限步后以非平局结束。

### 策梅洛定理

策梅洛定理[^cml]说明了，在**公平组合游戏**中，任意状态一定是胜态（先手必胜，N），或败态（后手必胜，P）。

### 必胜/必败状态

容易发现， $a$ 是必胜状态，当且仅当：

$ $\exists\,b\left(a \rightarrow b\right)\text{为必败状态}$ $ 

同样地， $a$ 是必败状态，当且仅当 $a$ 没有后继状态或者：

$ $\forall\,b\left(a \rightarrow b\right)\text{为必胜状态}$ $ 

## Nim游戏

$ $n$ 堆物品，每堆有 $a_i$ $ 个，两个玩家轮流取走任意一堆的任意个物品，不能不取。没有物品可取则失败。

### 判断必胜

$ $Nim和=\mathop\oplus\limits_{i=1}^{n}a_i$ $ 

即所有物品数的异或和。

当且仅当Nim和为 $0$ 时，该状态为必败状态；否则该状态为必胜状态。

### 证明（数学归纳法）

- $\forall a_i=0$ 时，显然必败。
  
记Nim和为 $m$ ，且 $m$ 二进制表示中为 $1$ 的最高位记为第 $k$ 位。那么：

- $\exists a_i$ 使得 $a_i$ 的第 $i$ 位为 $1$ 。
- 且 $a_i \oplus m < a_i$ ，因为 $a_i$ 的最高位变为 $0$ 。
- 则将 $a_i$ 改变为 $a_i \oplus m$ ，此时Nim和为 $m \oplus m = 0$ 。

由此，所有Nim和非零状态一定能转移到Nim和为零状态，而 $\forall a_i=0$ 的必败状态就是Nim和为零状态。


## 有向图游戏与SG函数

有向图游戏[^daggame]：

>在一个有向无环图中，只有一个起点，上面有一个棋子，两个玩家轮流沿着有向边推动棋子，不能走的玩家判负。

### SG定理

>定义[^sg] $\operatorname{mex}$ 函数的值为不属于集合 $S$ 中的最小非负整数，即：
>
>$$
>\operatorname{mex}(S)=\min\{x\} \quad (x \notin S, x \in N)
>$$
>
>例如 $\operatorname{mex}(\{0, 2, 4\})=1$ ， $\operatorname{mex}(\{1, 2\})=0$ 。
>
>对于状态 $x$ 和它的所有 $k$ 个后继状态 $y_1, y_2, \ldots, y_k$ ，定义 $\operatorname{SG}$ 函数：
>
>$$
>\operatorname{SG}(x)=\operatorname{mex}\{\operatorname{SG}(y_1), \operatorname{SG}(y_2), \ldots, \operatorname{SG}(y_k)\}
>$$
>
>而对于由 $n$ 个有向图游戏组成的组合游戏，设它们的起点分别为 $s_1, s_2, \ldots, s_n$ ，则有定理：**当且仅当 $\operatorname{SG}(s_1) \oplus \operatorname{SG}(s_2) \oplus \ldots \oplus \operatorname{SG}(s_n) \neq 0$ 时，这个游戏是先手必胜的。同时，这是这一个组合游戏的游戏状态 $x$ 的 SG 值。**
>
>这一定理被称作 SG 定理。

### 证明：
由 $\operatorname{mex}$ 函数的性质可知，所有SG值大的状态，一定能转移到所有SG值较小的状态。因此不用考虑向SG值较大的状态转移的情况（对手能转移回来）。

而因为每个状态都能转移到所有SG值小于自身的任意状态，这与Nim游戏中取走物品时等价的。因此SG定理得证。

### SG函数与Nim游戏

显然，在Nim游戏中，状态 $a_i$ 能到达的就是所有 $<a_i$ 的状态，所以有 $SG(a_i)=a_i$ ，因此Nim游戏的必胜判定方式与SG定理的内容相同。



[^gpzh]: <https://oi-wiki.org/math/game-theory/#_1>

[^cml]: <https://en.wikipedia.org/wiki/Zermelo's_theorem_(game_theory)> <https://zh.wikipedia.org/wiki/%E7%AD%96%E6%A2%85%E6%B4%9B%E5%AE%9A%E7%90%86_(%E5%8D%9A%E5%BC%88%E8%AB%96)>

[^daggame]: <https://oi-wiki.org/math/game-theory/#sg>

[^sg]:<https://oi-wiki.org/math/game-theory/#sg_1>
