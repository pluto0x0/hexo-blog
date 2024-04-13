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

Given policy $\pi_\theta$, optimize ${J}\left(\pi_\theta\right):=\mathbb{E}_{{s} \sim {d}_0}\left[{~V}^{\pi_\theta}({s})\right]$
 where $d_0$ is the initial state distribution.

- Use SGD ($\nabla_\theta {J}\left(\pi_\theta\right)$)
- an unbiased estimate can be obtained from a
**single on-policy trajectory**
- no need of the knowledge of $P$ and $R$ of the MDP
- Similar to [IS](/2024/03/23/reinforcement-learning-lecture-17/#Importance-Sampling)

About PG:

- Goal: we want to find good policy.
- Value-based RL is indirect
- PG isn't based on value function
  - It's possible a good policy don't match Bellman Equation.

### Example of policy parametrization

Linear + softmax:

- Featurize state-action: $\phi: {S} \times {A} \rightarrow \mathbb{R}^{{d}}$
- Policy (softmax): $\pi({a} \mid {s}) \propto {e}^{\theta^{\top} \phi({s}, {a})}$

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
& \nabla_\theta J\left(\pi_\theta\right) \\
= & \nabla_\theta \sum_{\tau \in(S \times A)^H} P^{\pi_\theta}(\tau) R(\tau) \\
= & \sum_\tau\left(\nabla_\theta P^{\pi_\theta}(\tau)\right) R(\tau) \\
= & \sum_\tau \frac{P^\pi(\tau)}{p^{\pi_\theta}(\tau)} \nabla_\theta P^{\pi_\theta}(\tau) R(\tau) \\
= & \sum_\tau p^{\pi}(\tau) \nabla_\theta \log P^{\pi_\theta}(\tau) R(\tau) \\
= & \mathbb{E}_{\tau \sim \pi}\left[\nabla_\theta \log P^{\pi_\theta}(\tau) R(\tau)\right]
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

> Note that this form is similar to that discussed in [Importance Sampling](/2024/03/24/reinforcement-learning-lecture-18/#Multi-step-IS-in-MDPs)
