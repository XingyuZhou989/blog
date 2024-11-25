---
comments: true
title: "Private mean estimation: pure vs approximate DP (I)"
updated: 2024-11-24 20:00
---
$$
\newcommand{\bA}{\mathbf{A}}
\newcommand{\bB}{\mathbf{B}}
\newcommand{\bC}{\mathbf{C}}
\newcommand{\cA}{\mathcal{A}}
\newcommand{\cR}{\mathcal{R}}

\newcommand{\bH}{\mathbf{H}}
\newcommand{\bI}{\mathbf{I}}
\newcommand{\bG}{\mathbf{G}}
\newcommand{\bZ}{\mathbf{Z}}
\newcommand{\Real}{\mathbb{R}}
\newcommand{\cN}{\mathcal{N}}
\newcommand{\norm}[1]{\left\lVert#1\right\rVert}
\newcommand{\ex}[1]{\mathbb{E}\left[ #1 \right] }
\newcommand{\cL}{\mathcal{L}}
$$

In this series of blog posts, we will talk about mean estimation under differential privacy (DP) constraints. One key theme of this series is to explore when there exist separation results between pure DP and approximate DP. Somewhat surprisingly, we will see that for some statistical tasks, relaxing from pure DP to approximate DP does not lead to utility improvement in the worst case. 

<div class="divider"></div>

As the first post in this series, we will first see a simple but important statistical task -- *bounded empirical mean estimation* --  where the separation between pure and approximate DP exists, i.e., utility under pure DP is strictly worse than that under approximate DP.

**Problem Setup.** We are given a set of $$n$$ data points $$\{x_i\}_{i=1}^n$$ in $$\Real^d$$, each with a bounded $$\ell_2$$ norm of $$L$$. The task is to privately estimate the empirical mean in the [central DP model](https://en.wikipedia.org/wiki/Differential_privacy). 

<div class="divider"></div>

### Upper Bounds
By standard Laplace mechanism for pure DP and Gaussian mechanism for approximate DP, we have the following utility upper bounds. 
> **Pure DP:** Let $$\widetilde{\theta}_n = \frac{1}{n}\sum_{i=1}^n x_i + n_{\mathrm{Lap}}$$, $$n_{\mathrm{Lap}} = (n_1,\ldots, n_d)$$, where $$n_i$$ is i.i.d sample from $$\mathrm{Lap}(\frac{2L\sqrt{d}}{\epsilon})$$. Then, let $$\theta^* := \frac{1}{n}\sum_{i=1}^n x_i$$, we have 
>
><a id="eq1"></a>
>$$ 
    \mathbb{E}[\norm{\widetilde{\theta}_n - {\theta^*}}_2^2] = O\left(\frac{L^2 d^2}{n^2 \epsilon^2}\right), \tag{1.1}
$$
>
>while satisfying $$\epsilon$$-DP. Further, by Jensen's inequality, we have
><a id="eq2"></a>
>$$
    \mathbb{E}[\norm{\widetilde{\theta}_n - {\theta^*}}_2] = O\left(\frac{L d}{n \epsilon}\right). \tag{1.2}
>$$
>
> **Approximate DP:** Let $$\widetilde{\theta}_n = \frac{1}{n}\sum_{i=1}^n x_i + n_{\mathrm{Gaus}}$$, $$n_{\mathrm{Gaus}} = (n_1,\ldots, n_d)$$, where $$n_i$$ is i.i.d sample from $$\mathcal{N}(0,\frac{8L^2\log(1.25/\delta) }{n^2\epsilon^2})$$. Then, let $${\theta^*} := \frac{1}{n}\sum_{i=1}^n x_i$$, we have 
><a id="eq3"></a>
>$$
        \mathbb{E}[\norm{\widetilde{\theta}_n - {\theta^*}}_2^2] = O\left(\frac{L^2 d \log(1/\delta)}{n^2 \epsilon^2}\right), \tag{1.3}
$$
>
while satisfying $$(\epsilon,\delta)$$-DP with $$\epsilon \in [0,1]$$. Similarly, by Jensen's inequality, we have 
><a id="eq4"></a>
$$
    \mathbb{E}[\norm{\widetilde{\theta}_n - {\theta^*}}_2] = O\left(\frac{L \sqrt{d\log(1/\delta)}}{n\epsilon}\right). \tag{1.4}
$$

Thus, we can see that relaxing to approximate DP allows us to improve the dependence on $$d$$ while paying a little cost on the order of $$\log(1/\delta)$$. In particular, roughly there exists a  $$O(d)$$ improvement in the square of $$\ell_2$$ norm (cf. [(1.1)](#eq1) and [(1.3)](#eq3)) and $$O(\sqrt{d})$$ in $$\ell_2$$ norm (cf. [(1.2)](#eq2) and [(1.4)](#eq4)).

However, at this moment, we cannot conclude that there indeed exists a separation between pure and approximate DP, given that the above are only upper bound results. Next, we will show that matching lower bounds exist, hence indicating the separation results.



<div class="divider"></div>

### Lower Bounds

We first leverage the lower bound for DP-ERM to *quickly* establish lower bounds in terms of the square of $$\ell_2$$ norm. To this end, we first define the following empirical loss functions

$$
    \hat{\cL}(\theta) = \frac{1}{2n}\sum_{i=1}^n \norm{\theta - x_i}_2^2,
$$

which attains its minimum value at $$\theta^* = \frac{1}{n}\sum_{i=1}^n x_i$$. Moreover, we have for any $$\theta$$,

$$
    \frac{1}{2} \norm{\theta - \theta^*}_2^2 = \hat{\cL}(\theta) - \hat{\cL}(\theta^*).
$$

Then, by Theorems 5.4 and 5.5 in [[BST14]](https://arxiv.org/pdf/1405.7085), we have the following lower bounds.

>**Pure DP:** Let $$n, d \in \mathbb{N}$$ and $$\epsilon >0$$.  For any $$\epsilon$$-DP estimator with output being $$\widetilde{\theta}_n$$, there is a dataset $$\{x_1,\ldots, x_n\}$$ where each $$x_i \in \{\pm \frac{1}{\sqrt{d}}\}^d$$ (which satisfies a bounded $$\ell_2$$ norm with $$L=1$$), such that with probability at least $$1/2$$, we must have 
>
$$
    \norm{\widetilde{\theta}_n - \theta^*}_2^2 = \Omega\left(\min\left\{ 1, \frac{d^2}{n^2 \epsilon^2}\right\}\right).
$$
>
>**Approximate DP:** Let $$n, d\in \mathbb{N}$$, $$\epsilon >0$$ and $$\delta = o(1/n)$$. For any $$(\epsilon,\delta)$$-DP estimator with output being $$\widetilde{\theta}_n$$, there is a dataset $$\{x_1,\ldots, x_n\}$$ where each $$x_i \in \{\pm \frac{1}{\sqrt{d}}\}^d$$ (which satisfies a bounded $$\ell_2$$ norm with $$L=1$$), such that with probability at least $$1/3$$, we must have 
>
$$
    \norm{\widetilde{\theta}_n - \theta^*}_2^2 = \Omega\left(\min\left\{ 1, \frac{d}{n^2 \epsilon^2}\right\}\right).
$$

**Remark.**  One can easily establish lower bounds for general $$L$$ with an additional factor of $$L^2$$.

From the lower bounds above, we can see that one has to pay an additional $$d$$ factor under pure DP. One limitation of the above lower bound is the missing dependence on $$\delta$$, for approximate DP. Next, we will show a tighter lower bound in terms of $$\ell_2$$ norm that incorporates $$\log(1/\delta)$$ term in the lower bound. This is based on tighter results for one-way marginal estimations established in [[SU15]](https://arxiv.org/pdf/1501.06095). In fact, the above lower bounds on DP-ERM are also based on the results for private one-way marginal estimations (a loop argument here:) ).

In particular, based on Lemma 5.1 in [[BST14]](https://arxiv.org/pdf/1405.7085) (for pure DP) and Theorem 1.1 in [[SU15]](https://arxiv.org/pdf/1501.06095) (for approximate DP), we have the following lower bounds.

>**Pure DP:** Let $$n, d \in \mathbb{N}$$ and $$\epsilon >0$$.  For any $$\epsilon$$-DP estimator with output being $$\widetilde{\theta}_n$$, there is a dataset $$\{x_1,\ldots, x_n\}$$ where each $$x_i \in \{\pm \frac{1}{\sqrt{d}}\}^d$$ (which satisfies a bounded $$\ell_2$$ norm with $$L=1$$), such that
>
$$
    \ex{\norm{\widetilde{\theta}_n - \theta^*}_2} = \Omega\left(\min\left\{ 1, \frac{d}{n \epsilon}\right\}\right).
$$
>
>**Approximate DP:** Let $$n, d\in \mathbb{N}$$, $$\epsilon \le O(1)$$ and $$ 2^{-O(n)} \le \delta \le o(1/n)$$. For any $$(\epsilon,\delta)$$-DP estimator with output being $$\widetilde{\theta}_n$$, there is a dataset $$\{x_1,\ldots, x_n\}$$ where each $$x_i \in \{\pm \frac{1}{\sqrt{d}}\}^d$$ (which satisfies a bounded $$\ell_2$$ norm with $$L=1$$), such that for $$n$$ large enough,  
>
$$
    \ex{\norm{\widetilde{\theta}_n - \theta^*}_2} = \Omega\left(\min\left\{ 1, \frac{\sqrt{d\log(1/\delta)}}{n \epsilon}\right\}\right).
$$

**Remark.** The bound for approximate DP follows from the conversion from sample complexity bound in [[SU15]](https://arxiv.org/pdf/1501.06095) to the stated concentration bound with an additional care of the normalization of $$1/\sqrt{d}$$.

<div class="divider"></div>

### Implications
By the standard reduction from ERM to mean estimation (in particular, one-way marginals), one can establish similar seperation results in DP-ERM for convex Lipschitz (and strongly convex) functions, between pure and approximate DP (e.g., [[BST14]](https://arxiv.org/pdf/1405.7085) and [[LLL24]](https://arxiv.org/pdf/2105.13637)). Further, by another standard reduction from DP-SCO to DP-ERM (or call it reduction from ERM to SCO), one may further show separations in DP-SCO for convex Lipschitz (and strongly convex) functions. These may be another series of posts:)


<div class="divider"></div>

As expected, we see that weaker approximate DP indeed leads to a better utility bound across several statistical tasks. However, as we will see in the next couple of posts, this gain may disappear when we consider some other tasks. Stay tuned!


**THE END**
{: .center}

<div class="divider"></div>

Now, it's time to take a break by appreciating the masterpiece of Monet.


{: .center}
![Monet](../assets/post_images/haystacks-1.jpg){:height="400px" width="500px"}

**Haystacks**
{: .center}
_courtesy of https://www.wikiart.org/_
{: .center}
