---
title: Hamilton Regime Switching Model
summary: Let us start Hamilton Filter!
date: 2023-10-10
math: true

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.

authors:
  - admin
  #- Ted

tags:
  - Academic
  - Markdown
---


## Hamilton Regime Switching Model

### Regime Switching model

Hamilton (1989) presents the regime switching model, which is so influential and is one of the main reference paper of so many academic papers. Let $s_t=0,1,2, \ldots, k$ denotes the state variable with $k$ regimes. In case of a two-state regime switching model, $s_t=0$ and $s_t=1$ can be interpreted as the expansion and recession states at time $t$. A $k$-state regime switching linear regression model has the following form.
$$
\begin{array}{rlrl}
    y_t=c_{s_t}+\beta_{s_t} x_t+\epsilon_{s_t, t}, & & \epsilon_{s_t, t} & \sim N\left(0, \sigma_{s_t}\right) \\
    & \Downarrow & & \\
    y_t=c_1+\beta_1 x_t+\epsilon_{1, t}, & & \epsilon_{1, t} \sim N\left(0, \sigma_1\right) \\
    y_t=c_2+\beta_2 x_t+\epsilon_{2, t}, & & \epsilon_{2, t} \sim N\left(0, \sigma_2\right) \\
    \cdots & & \\
    y_t=c_k+\beta_k x_t+\epsilon_{k, t}, & & \epsilon_{k, t} \sim N\left(0, \sigma_k\right)
\end{array}
$$
Since $s_t$ can take on $0,1, \ldots, k$, a transition probability matrix is introduced to describe their transitions.

### Markov Transition Probability Matrix

Each period, the regime or state follows Markov transition probability matrix. Markov means that transition probability depends on not long history of state transitions but only one lag. As examples, two- or three-state transition probability matrix are of the following forms.
two-state
$$
P\left(s_t=j \mid s_{t-1}=i\right)=P_{i j}=\left[\begin{array}{ll}
    p_{00} & p_{01} \\
    p_{10} & p_{11}
\end{array}\right]
$$
three-state
$$
P\left(s_t=j \mid s_{t-1}=i\right)=P_{i j}=\left[\begin{array}{lll}
    p_{00} & p_{01} & p_{02} \\
    p_{10} & p_{11} & p_{12} \\
    p_{20} & p_{21} & p_{22}
\end{array}\right]
$$
where $p_{i j}$ is the probability of transitioning from regime $i$ at time $t-1$ to regime $j$ at time $t$.

## Example

A two-state regime switching linear regression model and a transition probability matrix are of the following forms.
$$
\begin{aligned}
    & y_t=c_{s_t}+\beta_{s_t} x_t+\epsilon_{s_t, t}, \quad \epsilon_{s_t, t} \sim N\left(0, \sigma_{s_t}^2\right) \\
    & P\left(s_t=j \mid s_{t-1}=i\right)=P_{i j}=\left[\begin{array}{ll}
        p_{00} & p_{01} \\
        p_{10} & p_{11}
    \end{array}\right]
\end{aligned}
$$
Here $s_t$ can take on 0 or 1 and $p_{i j}$ is the probability of transitioning from regime $i$ at time $t-1$ to regime $j$ at time $t$.

### Hamilton Filtering

Hamilton filter is of the following sequence as we presented it in the previous post.

1.$t-1$ state (previous state)

$$
\xi_{i, t-1}=P\left(s_{t-1}=i \mid \bar{y}_{t-1} ; \theta\right)
$$

2.state transition from $i$ to $j$ (state propagation) $$p_{i j}$$

3.densities under the two regimes at $t$ (data observations and state dependent errors)

$$
\eta_{j t}=\frac{1}{\sqrt{2 \pi} \sigma} \exp \left[-\frac{\left(y_t-c_j-\beta_j x_t\right)^2}{2 \sigma_j^2}\right]
$$

4.conditional density of the time $t$ observation (combined likelihood with state being collapsed):

$$
\begin{aligned}
    f\left(y_t \mid \tilde{y}_{t-1} ; \theta\right) & =\xi_{0, t-1} p_{00} \eta_{0 t}+\xi_{0, t-1} p_{01} \eta_{1 t} \\
    & +\xi_{1, t-1} p_{10} \eta_{0 t}+\xi_{1, t-1} p_{11} \eta_{1 t}
\end{aligned}
$$

5.$t$ posterior state (corrected from previous state)

$$
\xi_{j t}=\frac{\sum_{i=0}^1 \xi_{i, t-1} p_{i j} \eta_{j t}}{f\left(y_t \mid \bar{y}_{t-1} ; \theta\right)}
$$

6.use posterior state at time $t$ as previous state a time $t+1$ (substitution)

$$
\xi_{j t} \rightarrow \xi_{i, t-1}
$$

7.iterate 1 $\sim$ 6 from $t=1$ to $T$
As a result of executing this iteration, the sample conditional log likelihood of the observed data can be calculated in the following way.

$$
\log f\left(\bar{y}_t \mid y_0 ; \theta\right)=\sum_{t=1}^T f\left(y_t \mid \bar{y}_{t-1} ; \theta\right)
$$
With this log-likelihood function, we use a numerical optimization to find the best fitting parameter set $(\bar{\theta})$.

### Numerical Optimization

To start iterations of the Hamilton filter, we need to set $\xi_{i, 0}$ and in most cases the unconditional state probabilities are used.
$$
\begin{aligned}
    & \xi_{0,0}=\frac{1-p_{11}}{2-p_{00}-p_{11}} \\
    & \xi_{1,0}=1-\xi_{0,0}
\end{aligned}
$$
