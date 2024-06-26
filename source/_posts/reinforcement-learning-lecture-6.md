---
title: Reinforcement Learning (6)
date: 2024-02-11 07:51:18
img_path: /_posts/
categories:
- Course Notes
- Reinforcement Learning
---

Recall $V^\star , Q^\star , V^\pi, Q^\pi$ .

$$
V^\star (s)=\max _{a \in A}(\underbrace{R(s, a)+\gamma \mathbb{E}_{S^{\prime} \sim P(\cdot \mid s, a)}\left[V^\star \left(s^{\prime}\right)\right]}_{Q^\star (s, a)})
$$

## Bellman Operator

$$
\begin{gathered}
\forall f: S\times A \to \mathbb{R}, \\
(\mathcal{T}f) (s,a) = (R(s,a) + \gamma \mathbb{E}_{s'\sim P(\cdot|s,a)}[\max_{a'} f(s', a')]) \\
\text{where} \; \mathcal{T}: \mathbb{R}^{SA} \to \mathbb{R}^{SA}.
\end{gathered}
$$

then

$$
\begin{gathered}
Q^\star  = \mathcal{T} Q^\star  \\
V^\star  = \mathcal{T} V^\star  \\
\end{gathered}
$$

i.e. $Q^\star$ and $V^\star$ are fixpoints of $\mathcal{T}$ .

## Value Interation Algorithm (VI)

$$
\text{funtion } f_0 = \vec{0} \in \mathbb{R}^{SA} 
$$

Interation to calculate fix points:

for $k = 1,2,3, \cdots$ ,

$$
f_k = \mathcal{T} f_{k-1}
$$

How to do interation

$$
\forall s,a: \; f_1(s,a) \leftarrow \mathcal{T} f_0(s,a)
$$

- synchronized iteration: use all functions values from old version during the update.
- asynchronized iteration

## Convergence of VI

lemma: $\mathcal{T}$ is a $\gamma$ **-contraction** under $\| \cdot\| _{\infty}$ where $\| \cdot\| _{\infty} := \max_{x\in (\cdot)}x$ .

which means 

$$
\| \mathcal{T}f-\mathcal{T}f'\| _{\infty}\leq\gamma\| f-f'\| _{\infty}
$$

Proof.

$$
\begin{aligned}
&\| f_k - Q^\star  \|_\infty  \\
=& \| \mathcal{T}f_{k-1} - Q^\star \|_\infty \\
=& \| \mathcal{T}f_{k-1} - \mathcal{T}Q^\star \|_\infty \\
\overset{\text{lemma}}{\le}& \gamma \| f_{k-1} - Q^\star \|_\infty \\
\end{aligned}
$$

$$
\Rightarrow \| f_k-Q^\star \|_\infty \le \gamma^k \| f_{k-1} -Q^\star \|_\infty \; \text{where}\; \gamma \in (0,1) \blacksquare
$$

Proof of lemma:

It suffies to prove 

$$
\begin{aligned}
&\left|(\mathcal{T}f - \mathcal{T}f') (s,a)\right| \\
= &\left|\left(R(s,a) + \gamma \mathbb{E}_{s'\sim P(\cdot|s,a)}[\max_{a'} f(s', a')]\right)
- \left(R(s,a) + \gamma \mathbb{E}_{s'\sim P(\cdot|s,a)}[\max_{a'} f(s', a')]\right)\right| \\
=& \gamma\left|\mathbb{E}_{s'\sim P(\cdot|s,a)}[\max_{a'} f(s', a') - \max_{a'} f'(s', a')]\right|
\end{aligned}
$$

$\text{WLOG} $assume,$ \max_{a'} f(s', a') > \max_{a'} f'(s', a') $and$ \exist a^\star : \max_af(s',a)=f(s',a^\star )$, then

$$
\begin{aligned}
& \gamma\left|\mathbb{E}_{s'\sim P(\cdot|s,a)}[\max_{a'} f(s', a') - \max_{a'} f'(s', a')]\right| \\
=& \gamma\left|\mathbb{E}_{s'\sim P(\cdot|s,a)}[f(s', a^\star ) - \max_{a'} f'(s', a')]\right| \\
\le& \gamma\left|\mathbb{E}_{s'\sim P(\cdot|s,a)}[f(s', a^\star ) - f'(s', a^\star )]\right| \\
\le& \gamma\left|f(s', a^\star ) - f'(s', a^\star )\right| \\
\le& \gamma \| f-f'\|_\infty
\end{aligned}
$$