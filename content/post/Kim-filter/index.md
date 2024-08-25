---
title: Example of Regime Switching State Space Model
summary: Enjoy Kim Filter!
date: 2023-10-27
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

{{< toc mobile_only=true is_open=true >}}

## Regime Switching State Space Model

### Model Description

As an example of a regime switching state space model, Prof. Kim used the following generalized Hamilton model for the log of real GNP (Lam; 1990) in his paper and book.

$$
\begin{aligned}
    \ln \left(G N P_t\right) & =n_t+x_t \\
    n_t & =n_{t-1}+\mu_0+\mu_1 s_t \\
    x_t & =\phi_1 x_{t-1}+\phi_2 x_{t-2}+u_t \\
    u_t & \sim N\left(0, \sigma^2\right) \\
    s_t & =0,1 \quad P_{t j}=\left[\begin{array}{ll}
        p_{00} & p_{01} \\
        p_{10} & p_{11}
    \end{array}\right]
\end{aligned}
$$

where $\ln \left(G N P_t\right)$ is a real GNP level. $n_t$ is a deterministic series with a regimeswitching growth rate and $x_t$ is stationary AR(2) cycle process.

Since $\ln \left(G N P_t\right)$ is the log level variable, the difference of it, $y_t=\ln \left(G N P_t\right)-\ln \left(G N P_{t-1}\right)$, can be represented as a state space model in the following way.

$$\begin{aligned}
    & y_t=\mu_0+\mu_1 s_t+x_t-x_{t-1} \\
    & x_t=\phi_1 x_{t-1}+\phi_2 x_{t-2}+u_t
\end{aligned}
$$
It is a typical approach that a state space model is represented as a vector-matrix form for using Kalman filter as follows.
$$
\begin{aligned}
    y_t & =\mu_{s_t}+\left[\begin{array}{ll}
        1 & -1
    \end{array}\right]\left[\begin{array}{c}
        x_t \\
        x_{t-1}
    \end{array}\right] \\
    {\left[\begin{array}{c}
            x_t \\
            x_{t-1}
        \end{array}\right] } & =\left[\begin{array}{cc}
        \phi_1 & \phi_2 \\
        1 & 0
    \end{array}\right]\left[\begin{array}{c}
        x_{t-1} \\
        x_{t-2}
    \end{array}\right]+\left[\begin{array}{c}
        u_t \\
        0
    \end{array}\right] \\
    u_t & \sim N\left(0, \sigma^2\right) \\
    \mu_{s_t} & =\mu_0+\mu_1 s_t, \quad \mu_1>0 \\
    s_t & =0,1 \quad P_{t j}=\left[\begin{array}{ll}
        p_{00} & p_{01} \\
        p_{10} & p_{11}
    \end{array}\right] \\
    \Downarrow & \\
    y_t & =\mu_{s_t}+F \mathbf{x}_t \\
    \mathbf{x}_t & =A \mathbf{x}_{t-1}+v_t
\end{aligned}$$

For the sake of notational simplicity, we use $x_t$ instead of $\mathbf{x}_t$.

### Kalman Filtering

Kalman filter with regime switching is used to get state estimates from a state space model taking regime transition into account and has the following recursion.

$$
\begin{aligned}
    x_{t \mid t-1}^{i j} & =A x_{t-1 \mid t-1}^i \\
    P_{t \mid t-1}^{i j} & =A P_{t-1 \mid t-1}^i A^T+Q \\
    \eta_{t \mid t-1}^{i j} & =y_t-\mu_j-F x_{t \mid t-1}^{i j} \\
    H_{t \mid t-1}^{i j} & =F P_{t \mid t-1}^{i j} F^T+R \\
    K^{i j} & =P_{t \mid t-1}^{i j} F^T\left[H_{t \mid t-1}^{i j}\right]^{-1} \\
    x_{t \mid t}^{i j} & =x_{t \mid t-1}^{i j}+K^{i j} \eta_{t \mid t-1}^{i j} \\
    P_{t \mid t}^{i j} & =\left(I-K^{i j} F\right) P_{t \mid t-1}^{i j}
\end{aligned}
$$
In regime-dependent Kalman filter, all the notations are augmented with superscript $\{i j\}$ except $x_{t-1 \mid t-1}^i$ and $P_{t-1 \mid t-1}^i$ since these two estimates are in i-state (two-state) but other estimates must reflect state transitions from i to $\mathrm{j}$ (four-state). For example, $x_{t \mid t-1}$ and $x_{t \mid t-1}^{i j}$ are different in terms of conditioning information.
$$
\begin{aligned}
    x_{t \mid t-1} & =E\left[X_t \mid \psi_{t-1}\right] \\
    x_{t \mid t-1}^{i j} & =E\left[X_t \mid \psi_{t-1}, S_t=j, S_{t-1}=i\right]
\end{aligned}
$$
In contrast to the single regime, however, in the multiple regimes, $x_{t \mid t}^{i j}$ and $P_{t \mid t}^{i j}$ cannot be used the next state prediction due simply to the mismatch both 1) between $x_{t \mid t}^{i j}$ and $x_{t-1 \mid t-1}^i$ and 2) between $P_{t \mid t}^{i j}$ and $P_{t-1 \mid t-1}^i$. To resolve this mismatch problem, Kim (1994) developed a dimension collapsing algorithm.

## Kim(1994)’s Collapsing procedure

Kim (1994) introduces a collapsing procedure (approximation) to reduce the $(M \times M)$ posteriors $\left(x_{t \mid t}^{i j}\right.$ and $P_{t \mid t}^{i j}$ ) into $M$ to complete the above Kalman filter recursion.
$$
\begin{aligned}
    x_{t \mid t}^j & =\frac{\sum_{i=1}^M P\left[S_{t-1}=i, S_t=j \mid \psi_t\right] x_{t \mid t}^{i j}}{P\left[S_t=j \mid \psi_t\right]} \\
    P_{t \mid t}^j & =\frac{\sum_{i=1}^M P\left[S_{t-1}=i, S_t=j \mid \psi_t\right]\left[P_{t \mid t}^{i j}+\left(x_{t \mid t}^j-x_{t \mid t}^{i j}\right)\left(x_{t \mid t}^j-x_{t \mid t}^{i j}\right)^T\right]}{P\left[S_t=j \mid \psi_t\right]}
\end{aligned}
$$
To calculate the above approximation, when we calculate $P\left[S_{t-1}=i, S_t=j \mid \psi_t\right]$, $P\left[S_t=j \mid \psi_t\right]$ is easily obtained by summing its $\mathrm{M}$ branches from each i states.
$$
P\left[S_t=j \mid \psi_t\right]=\sum_{i=1}^M P\left[S_{t-1}=i, S_t=j \mid \psi_t\right]
$$
Knowing $P\left[S_{t-1}=i, S_t=j \mid \psi_t\right]$ means that we observe time t data since the last time of information set is $t$ and the state is migrated from $i$ to $j$. For this data information into account, we can think of the marginal probability of state transition by integrating out data.
$$
\begin{aligned}
    & P\left[S_{t-1}=i, S_t=j \mid \psi_t\right]=\frac{f\left(y_t, S_{t-1}=i, S_t=j \mid \psi_{t-1}\right)}{f\left(y_t \mid \psi_{t-1}\right)} \\
    & f\left(y_t \mid \psi_{t-1}\right)=\sum_{j=1}^M \sum_{i=1}^M f\left(y_t, S_{t-1}=i, S_t=j \mid \psi_{t-1}\right)
\end{aligned}
$$
As can be seen from the above equations, when we know $f\left(y_t, S_{t-1}=i, S_t=j \mid \psi_{t-1}\right)$ which is the joint density of data and two states, $P\left[S_{t-1}=i, S_t=j \mid \psi_t\right]$ and $f\left(y_t \mid \psi_{t-1}\right)$ are easily obtained. We get the following the joint density.
$$
\begin{aligned}
    & f\left(y_t, S_{t-1}=i, S_t=j \mid \psi_{t-1}\right) \\
    & =f\left(y_t \mid S_{t-1}=i, S_t=j, \psi_{t-1}\right) \times P\left(S_{t-1}=i, S_t=j \mid \psi_{t-1}\right)
\end{aligned}
$$
Now we need to know two parts. The first part is the forecast error given data. (MVN : probability density function of multivariate normal distribution with zero mean and forecast error variance)
$$
\begin{aligned}
    & f\left(y_t \mid S_{t-1}=i, S_t=j, \psi_{t-1}\right) \\
    & =M V N \text { (forecast error, its variance) }
\end{aligned}
$$
The second part is calculated by the multiplication of the transition probability and the summation of its branches.
$$
\begin{aligned}
    & P\left(S_{t-1}=i, S_t=j \mid \psi_{t-1}\right) \\
    & =P\left[S_t=j \mid S_{t-1}=i\right] \times \sum_{k=1}^M f\left(S_{t-2}=k, S_{t-1}=i \mid \psi_{t-1}\right) \\
    & =P_{i j} \times f\left(S_{t-1}=i \mid \psi_{t-1}\right)
\end{aligned}
$$
In the above equation, as we know, $P_{i j}$ is already known as transition probability matrix and $f\left(S_{t-2}=k, S_{t-1}=i \mid \psi_{t-1}\right)$ is exactly what we want to find but evaluated at the previous t- 1 time. Therefore for the iteration, $f\left(S_{-1}=k, S_0=i \mid \psi_0\right)$ calls for initialization with the steady state probabilities.
Therefore, we can calculate $P\left[S_{t-1}=i, S_t=j \mid \psi_t\right]$ and $P\left[S_t=j \mid \psi_t\right]$ through the above equations.

## Kim (1994) Filter for Regime Switching State Space model 

Kim filtering procedure is summarized in a sequence of equations.
Kalman Filtering
$$
\begin{aligned}
    x_{t \mid t-1}^{i j} & =A x_{t-1 \mid t-1}^i \\
    P_{t \mid t-1}^{i j} & =A P_{t-1 \mid t-1}^i A^T+Q \\
    \eta_{t \mid t-1}^{i j} & =y_t-\mu_j-F x_{t \mid t-1}^{i j} \\
    H_{t \mid t-1}^{i j} & =F P_{t \mid t-1}^{i j} F^T+R \\
    K^{i j} & =P_{t \mid t-1}^{i j} F^T\left[H_{t \mid t-1}^{i j}\right]^{-1} \\
    x_{t \mid t}^{i j} & =x_{t \mid t-1}^{i j}+K^{i j} \eta_{t \mid t-1}^{i j} \\
    P_{t \mid t}^{i j} & =\left(I-K^{i j} F\right) P_{t \mid t-1}^{i j} 
\end{aligned}
$$
$$\Downarrow$$
<center>Hamilton Filtering</center>
$$
\begin{aligned}
    & f\left(y_t, S_{t-1}=i, S_t=j \mid \psi_{t-1}\right)=N\left(\eta_{t \mid t-1}^{i j}, H_{t \mid t-1}^{i j}\right) \times P_{i j} \times P\left(S_{t-1}=i \mid \psi_{t-1}\right) \\
    & f\left(y_t \mid \psi_{t-1}\right)=\sum_{j=1}^M \sum_{i=1}^M f\left(y_t, S_{t-1}=i, S_t=j \mid \psi_{t-1}\right) \\
    & P\left[S_{t-1}=i, S_t=j \mid \psi_t\right]=\frac{f\left(y_t, S_{t-1}=i, S_t=j \mid \psi_{t-1}\right)}{f\left(y_t \mid \psi_{t-1}\right)} \\
    & P\left[S_t=j \mid \psi_t\right]=\sum_{i=1}^M P\left[S_{t-1}=i, S_t=j \mid \psi_t\right] \\
\end{aligned}
$$
$$\Downarrow$$
<center>Kim's Collapsing</center>
$$
\begin{aligned}
    x_{t \mid t}^j & =\frac{\sum_{i=1}^M P\left[S_{t-1}=i, S_t=j \mid \psi_t\right] x_{t \mid t}^{i j}}{P\left[S_t=j \mid \psi_t\right]} \\
    P_{t \mid t}^j & =\frac{\sum_{i=1}^M P\left[S_{t-1}=i, S_t=j \mid \psi_t\right]\left[P_{t \mid t}^{i j}+\left(x_{t \mid t}^j-x_{t \mid t}^{i j}\right)\left(x_{t \mid t}^j-x_{t \mid t}^{i j}\right)^T\right]}{P\left[S_t=j \mid \psi_t\right]}
\end{aligned}
$$

In particular, three red colored terms $\left(x_{t-1 \mid t-1}^i, P_{t-1 \mid t-1}^i\right.$, and $\left.P\left(S_{t-1}=i \mid \psi_{t-1}\right)\right)$ are initialized for iteration to be started. Once the iterations get started, these red colored terms are replaced with blue colored terms $\left(x_{t \mid t}^j, P_{t \mid t}^j\right.$, and $\left.P\left(S_t=j \mid \psi_t\right)\right)$ for each iterations.
