---
title: Reinforcemant Learning (19)
date: 2024-04-12 09:43:00
img_path: /_posts/
mermaid: true
categories:
- Course Notes
- Reinforcement Learning
---

## Policy Gradient

Given policy $\pi_\theta$, optimize ${J}\left(\pi_\theta\right):=\mathbb{E}_{s\sim d_0}\left[{~V}^{\pi_\theta}(s)\right]$
 where $d_0$ is the initial state distribution.

- Use Gradient Ascent ($\nabla_\theta {J}\left(\pi_\theta\right)$)
- an unbiased estimate can be obtained from a
**single on-policy trajectory**
- no need of the knowledge of $P$ and $R$ of the MDP
- Similar to [IS](/2024/03/23/reinforcement-learning-lecture-17/#Importance-Sampling)

{% note info %}
Note that when we use $\pi$, we mean $\pi_{\theta}$ here, and $\nabla$ means $\nabla_\theta$.
{% endnote %}

About PG:

- Goal: we want to find good policy.
- Value-based RL is indirect
- PG isn't based on value function
  - It's possible a good policy don't match Bellman Equation.

### Example of policy parametrization

Linear + softmax:

- Featurize state-action: $\phi: S\times A \rightarrow \mathbb{R}^d$
- Policy (softmax): $\pi(a|s) \propto e^{\theta^{\top} \phi(s, a)}$

Recall in SARSA, we also used [softmax](/2024/03/22/reinforcement-learning-lecture-15/#softmax) with temperature $T$. But in PG, we don't need it. Why?

- In SARSA, softmax policy based on $Q$ function -- $Q$ function cannot be arbitrary.
- In PG, $\phi(s,a)$ is arbitrary function -- $T$ is included.

### PG Derivation

- The trajectory inducded by $\pi$: $\tau:=\left(s_1, a_1, r_1, \ldots, s_{H}, a_{H}, r_{H}\right)$ and $\tau \sim \pi$.
- Let $R(\tau):=\sum_{t=1}^H \gamma^{t-1} r_t$

#### Version 1

$$
J(\pi) := \mathbb{E}_\pi \left[\sum_{t=1}^H \gamma^{t-1}r_t\right] = \mathbb{E}_{\tau\sim\pi} [R(\tau)]
$$

$$
\begin{aligned}
& \nabla J\left(\pi\right) \\
= & \nabla \sum_{\tau \in(S \times A)^H} P^{\pi}(\tau) R(\tau) \\
= & \sum_\tau\left(\nabla P^{\pi}(\tau)\right) R(\tau) \\
= & \sum_\tau \frac{P^\pi(\tau)}{P^{\pi}(\tau)} \nabla P^{\pi}(\tau) R(\tau) \\
= & \sum_\tau P^{\pi}(\tau) \nabla \log P^{\pi}(\tau) R(\tau) \\
= & \mathbb{E}_{\tau \sim \pi}\left[\nabla \log P^{\pi}(\tau) R(\tau)\right]
\end{aligned}
$$

and

$$
\begin{aligned}
& \nabla_\theta \log P^{\pi_\theta}(\tau)
=\nabla_\theta \log \left(d_0\left(s_1\right) \pi\left(a_1 | s_1\right) P\left(s_2 | s_1, a_1\right) \pi\left(a_2 | s_2\right) \cdots \right) \\
&=
\cancel{\nabla \log d_0(s_1)} +
\nabla \log \pi\left(a_1 | s_1\right) +
\cancel{\nabla \log P\left(s_2 | s_1, a_1\right)} +
\nabla \log \pi\left(a_2 | s_2\right) +
\cdots \\
&=
\nabla \log \pi\left(a_1 | s_1\right) +
\nabla \log \pi\left(a_2 | s_2\right) +
\cdots
\end{aligned}
$$

Note that this form is similar to that discussed in [Importance Sampling](/2024/03/24/reinforcement-learning-lecture-18/#Multi-step-IS-in-MDPs).

Given that $\pi(a|s) = \frac{e^{\theta^\top \phi(s,a)}}{\sum_{a'} e^{\theta^\top \phi(s,a')}}$ (denoted by $\pi(a|s)$).

$$
\begin{aligned}
& \nabla \log \pi(a|s) \\
= & \nabla\left(\log \left(e^{\theta^\top \phi\left(s, a^{\prime}\right)}\right)-\log \left(\sum_{a^{\prime}} e^{\theta^{\top} \phi\left(s, a^{\prime}\right)}\right)\right) \\
= & \phi(s, a)-\frac{\sum_{a'} e^{\theta^\top \phi(s,a')}\phi(s,a')}{\sum_{a^{\prime}} e^{\theta^{\top} \phi\left(s, a^{\prime}\right)}} \\
= & \phi(s,a) - \mathbb{E}_{a'\sim \pi} [\phi(s,a')]
\end{aligned}
$$

Note that the expectation of the quantity above is $0$. i.e.

$$
\mathbb{E}_{a\sim\pi}\big[ \phi(s,a) - \mathbb{E}_{a'\sim \pi} [\phi(s,a')] \big] = 0
$$

**Couclusion**:

So far we have

$$
\nabla J(\pi) = 
\mathbb{E}_{\pi} \left[
  \left(\sum_{t=1}^H \gamma^{t-1} r_t \right)
  \left(\sum_{t=1}^H \nabla\log\pi (a_t|s_t) \right)
\right]
$$

With the relation discussed above, we say $\mathbb{E}_{\pi}[\nabla\log\pi (a_t|s_t)] = \sum_{a_t}\nabla \pi (a_t|s_t) = \nabla 1 = 0$

So, for $t' < t$, $r_{t'}$ is independent to $\nabla\log\pi (a_t|s_t)$, we have

$$
\mathbb{E}_{\pi}[\nabla\log\pi (a_t|s_t)r_{t'}]
= \mathbb{E}_{\pi}[\nabla\log\pi(a_t|s_t)] \mathbb{E}_{\pi}[r_{t'}]
= 0
$$

We can therefore rewrite the $\nabla J(\pi)$ as

$$
\nabla J(\pi) = 
\mathbb{E}_{\pi} \left[
  \sum_{t=1}^H \left(
    \nabla\log\pi (a_t|s_t)
    \sum_{t'=t}^H \gamma^{t'-1} r_{t'}
  \right)
\right]
$$

