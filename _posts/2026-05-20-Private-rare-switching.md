---
comments: true
title: "When Determinants Are Not Enough: Private Rare Switching"
updated: 2026-05-20 23:30
---
$$
\newcommand{\Real}{\mathbb{R}}
\newcommand{\norm}[1]{\left\lVert#1\right\rVert}
\newcommand{\wt}{\widetilde}
$$

In this post, I would like to share a small research moment where Codex helped me find the right way to adapt rare switching to the private setting. The standard determinant-based update rule in linear bandits and RL works beautifully because the design matrix grows monotonically. But once Gaussian noise is added for privacy, this monotonicity can fail, and the usual analysis no longer goes through. The key reason is that determinant growth controls volume, while regret analysis needs control of the worst direction. To address this, Codex comes up with a  different rare-switching rule based on the generalized Rayleigh quotient, which restores logarithmic policy updates and the desired confidence-width comparison up to a constant  factor.

A PDF version of this note is available [here](https://xingyuzhou.org/documents/Private_Rare_Switching.pdf).[^markdown]

**Note.** For better math rendering, right click any equation and choose `Math Settings` → `Math Renderer` → `Common HTML`.

<div class="divider"></div>

### The Problem

The problem is a variant of the standard rare-switching policy update in linear bandit and RL. Let us first recall the standard setting, i.e., Sec. 5.1 in [[APS11]](https://sites.ualberta.ca/~szepesva/papers/linear-bandits-NeurIPS2011.pdf). The learner maintains a positive-definite covariance matrix $$V_t = \lambda I + \sum_{s=1}^t x_s x_s^{\top}$$ for $$t \in [T]$$ and $$\lambda >0$$, and only updates its policy when

$$
    \det(V_t) > \alpha \det(V_{\tau})
$$

for some $$\alpha >1$$, where $$\tau$$ is the latest update time before $$t$$.

This simple rule is often called a *rare-switching update*. It can (i) reduce the number of total policy updates to $$O(\log T)$$, which directly follows from the standard elliptical potential lemma, Lemma 11 in [[APS11]](https://sites.ualberta.ca/~szepesva/papers/linear-bandits-NeurIPS2011.pdf), while (ii) only suffering a constant-factor blow-up in regret, which directly follows from Lemma 12 in [[APS11]](https://sites.ualberta.ca/~szepesva/papers/linear-bandits-NeurIPS2011.pdf). In particular, Lemma 12 in [[APS11]](https://sites.ualberta.ca/~szepesva/papers/linear-bandits-NeurIPS2011.pdf) implies that if $$V_t \succeq V_{\tau}$$, then for any $$x \in \Real^d$$,

$$
    \norm{x}_{V_{\tau}^{-1}} \le \sqrt{\alpha} \norm{x}_{V_t^{-1}}.
$$

Now, what if the covariance matrix is perturbed, say, due to privacy protection? For example, in private linear contextual bandits or linear RL, a Gaussian noisy matrix is often added to the non-private one to arrive at the private one:

$$
    \wt{V}_t = V_t + E_t,
$$

where $$E_t$$ is a symmetric matrix with elements sampled from a Gaussian distribution. A natural question to ask is: Can we still apply the above update rule, now on $$\wt{V}_t$$, along with its analysis to arrive at the same performance, i.e., log-order updates with the same-order regret?

The answer is no. The reason is that to apply Lemma 12, we have to guarantee the monotonicity property

$$
    \wt{V}_t \succeq \wt{V}_{\tau}.
$$

This is no longer true due to the private noise. This issue has been pointed out in my previous work [[CZ22]](https://proceedings.mlr.press/v162/chowdhury22a/chowdhury22a.pdf) (cf. Sec. 6) and also by my most recent work [[HZ26]](https://arxiv.org/pdf/2605.07049) (cf. Appendix C).

So, we can ask: how about a new rare-switching rule for the private case?[^old-rule]

<div class="divider"></div>

### The New Rule

I basically presented the above as a prompt to Codex 5.5 (Extra High) with all related papers in the folder. The first attempt of Codex gave me the correct answer, which is summarized in the following theorem[^1].

> **Theorem.** Let $$x_1,\ldots, x_T \in \Real^d$$ satisfy $$\norm{x_t}_2 \le L$$ for all $$t \in [T]$$. Let the non-private design matrices be
>
> $$
>     V_1 := \lambda I, \qquad V_{t+1}:= V_t + x_t x_t^{\top}.
> $$
>
> Assume the private matrices are $$\wt{V}_t = V_t  + E_t$$, where $$E_t$$ is a symmetric matrix such that for all $$t \in [T]$$[^2],
>
> $$
>     \norm{E_t}_2 \le \eta \quad \text{and} \quad 0\le 2 \eta  \le \lambda.
> $$
>
> Suppose that for any $$t \in [T]$$, the policy is updated when
>
> $$
>     \lambda_{\max}\left(\wt{V}_{\tau_t}^{-1/2} \wt{V}_t \wt{V}_{\tau_t}^{-1/2}\right) > \alpha,
> $$
>
> where $$\tau_t$$ is again the most recent update before $$t$$. Then, let $$\rho:= \eta /\lambda$$ and $$c_{\rho}:= \frac{1+\rho}{1-\rho}$$. We have:
>
> **(i)** For any $$x \in \Real^d$$ and $$t \in [T]$$,
>
> $$
>     \norm{x}_{\wt{V}_{\tau_t}^{-1}} \le \sqrt{\alpha} \norm{x}_{\wt{V}_t^{-1}}
>     \quad \text{and} \quad
>     \norm{x}_{V_{\tau_t}^{-1}} \le \sqrt{c_{\rho} \alpha} \norm{x}_{V_t^{-1}}.
> $$
>
> **(ii)** The total number of updates $$m$$ is upper bounded by
>
> $$
>     m \lesssim d \log_{\alpha/c_{\rho}} \left(1 + \frac{T L^2}{\lambda d}\right) = O_d(\log T).
> $$

*Remark.* Several remarks are in order. First, in most cases, $$\lambda$$ is set to be $$2\eta$$ so that the private design matrix is also positive definite; see the proof of Lemma A.4 in [[CZ22]](https://proceedings.mlr.press/v162/chowdhury22a/chowdhury22a.pdf). Hence, $$\rho = 1/2$$ and $$c_{\rho} = 3$$. Now, with the above update rule and guarantees, we have the right way to do rare switching in private linear bandit and RL. Second, note that we often only need the first inequality in (i) to bound the regret. Finally, we also note that the new update rule is also equivalent to that the learner updates iff $$\wt{V}_t \npreceq  \alpha \wt{V}_{\tau_t}$$, which is more expensive in computation compared with the old determinant-based update rule, which can leverage the efficient rank-one update.

*Proof.* We first present the following claim, which will be useful for the proof. The proof of the claim is given at the end.

> **Claim.** For $$1\le a \le b \le T$$, let
>
> $$
>     g_{a,b}:= \lambda_{\max}\left({V}_{a}^{-1/2} {V}_b {V}_{a}^{-1/2}\right)
> $$
>
> and
>
> $$
>     \wt{g}_{a,b}:= \lambda_{\max}\left(\wt{V}_{a}^{-1/2} \wt{V}_b \wt{V}_{a}^{-1/2}\right).
> $$
>
> Then,
>
> $$
>     c_{\rho}^{-1} g_{a,b} \le \wt{g}_{a,b} \le c_{\rho} g_{a,b}.
> $$

We start with (i). For any $$\tau_t < t$$, i.e., $$t$$ is not an update slot, the first inequality follows directly from the update rule.
For the second one, by the claim and our update rule, we first have

$$
    \lambda_{\max}\left({V}_{\tau_t}^{-1/2} {V}_t {V}_{\tau_t}^{-1/2}\right) \le c_{\rho} \alpha.
$$

Further, by the identity of generalized Rayleigh quotient,

$$
    \sup_{x \neq 0} \frac{x^{\top} V_t x}{x^{\top} V_{\tau_t} x}
    = \lambda_{\max}\left({V}_{\tau_t}^{-1/2} {V}_t {V}_{\tau_t}^{-1/2}\right)
    \le c_{\rho} \alpha.
$$

This implies that $$V_{\tau_t}^{-1} \preceq c_{\rho} \alpha V_t^{-1}$$, and hence

$$
    \norm{x}_{V_{\tau_t}^{-1}} \le \sqrt{c_{\rho} \alpha} \norm{x}_{V_t^{-1}}.
$$

We now move to (ii). Consider two adjacent policy update slots $$i < j$$. By the update rule and the claim, we have

$$
    \lambda_{\max}\left({V}_{i}^{-1/2} {V}_j {V}_{i}^{-1/2}\right) >  \alpha /c_{\rho}.
$$

Further, since $$V_j \succeq V_i$$, all eigenvalues of $${V}_{i}^{-1/2} {V}_j {V}_{i}^{-1/2}$$ are at least one. Hence, by the above result and multiplicativity of determinant,

$$
    \frac{\det(V_j)}{\det(V_i)}
    = \det\left({V}_{i}^{-1/2} {V}_j {V}_{i}^{-1/2}\right)
    > \alpha /c_{\rho}.
$$

Thus, by the elliptical potential lemma, Lemma 11 in [[APS11]](https://sites.ualberta.ca/~szepesva/papers/linear-bandits-NeurIPS2011.pdf), the total number of updates $$m$$ is upper bounded by

$$
    m \lesssim d \log_{\alpha/c_{\rho}} \left(1 + \frac{T L^2}{\lambda d}\right).
$$

We are left with the proof of the claim. To show it, we first notice that by the condition on $$E_t$$,

$$
    -\eta I \preceq E_t \preceq \eta I.
$$

Further, by $$V_t \succeq \lambda I$$, we have $$\eta I \preceq (\eta/\lambda) V_t = \rho V_t$$. Combining the above yields

$$
    (1-\rho) V_t \preceq \wt{V}_t \preceq (1+\rho) V_t.
$$

Thus, for any nonzero vector $$x \in \Real^d$$,

$$
    \frac{x^{\top} \wt{V}_b x}{x^{\top} \wt{V}_a x}
    \le
    \frac{(1+\rho) x^{\top} {V}_b x}{(1-\rho) x^{\top} {V}_a x}
    =
    c_{\rho} \frac{x^{\top} {V}_b x}{x^{\top} {V}_a x}.
$$

Taking the supremum and applying the identity of generalized Rayleigh quotient yields $$\wt{g}_{a,b} \le c_{\rho} g_{a,b}$$. The other direction follows from the same proof. $$\Box$$

<div class="divider"></div>

### The Connection

One may wonder what the difference or connection is between the new update rule and the standard one. To see this, it is instructive to examine the behavior of the new update rule even in the non-private case, and ask whether it leads to more frequent or less frequent updates. To this end, I directly prompted Codex, also with ChatGPT, with the screenshot of Lemma 12 in [[APS11]](https://sites.ualberta.ca/~szepesva/papers/linear-bandits-NeurIPS2011.pdf). It then gave me a new proof which actually sheds a lot of light on how Codex came up with the new update rule and its connection with the old one.

The new proof is much simpler, at least to me, compared with the original proof. Basically, the proof first notes that the left-hand side in Lemma 12 is equal to

<a id="eq-new"></a>
$$
    \sup_{x \neq 0} \frac{x^{\top}A x}{x^{\top} B x}
    =
    \lambda_{\max}(B^{-1/2} A B^{-1/2}),
    \tag{3.1}
$$

where again we apply the identity of generalized Rayleigh quotient, while the right-hand side in Lemma 12 is equal to

<a id="eq-old"></a>
$$
    \frac{\det(A)}{\det(B)}
    =
    \det(B^{-1/2} A B^{-1/2}).
    \tag{3.2}
$$

Note that [(3.1)](#eq-new) $$\le$$ [(3.2)](#eq-old) by the fact that all eigenvalues of $$B^{-1/2} A B^{-1/2}$$ are at least one, which comes from the monotonicity $$B \preceq A$$. This proves Lemma 12.

So, essentially, the new update rule comes from the new proof of Lemma 12, i.e., [(3.1)](#eq-new). Moreover, under the new update rule, the learner updates less frequently, since [(3.1)](#eq-new) $$> \alpha$$ implies [(3.2)](#eq-old) $$> \alpha$$, but not the other direction.

This new proof also explains why the new update rule can get rid of the issue in the old rule when lacking monotonicity. This is because using the old update rule, cf. [(3.2)](#eq-old), one has to further leverage monotonicity to arrive at the bound on $$\sup_{x \neq 0} \frac{x^{\top}A x}{x^{\top} B x}$$, i.e., the worst-case direction. On the other hand, the new rule directly works with $$\sup_{x \neq 0} \frac{x^{\top}A x}{x^{\top} B x}$$.

We can now also see that with the old update rule, it is impossible to control the worst-case direction without monotonicity since the eigenvalues in other directions can be much smaller and hence a very large value in the worst-case direction can still make the determinant small.

[^1]: I cleaned up and re-organized the original proof of Codex, which was correct but not that easy to read.
[^2]: For Gaussian matrices, this condition holds with high probability.
[^old-rule]: Of course, one may ask if we can keep the same update rule but with a more advanced analysis to maintain the same performance guarantees. It turns out that this is not possible, which will become clear at the end of this post.
[^markdown]: In fact, this markdown file is directly generated from my source `.tex` file by Codex.
[^monet]: In fact, this figure is also chosen by Codex with the following reasoning: the post is about how the usual determinant/volume view becomes unreliable once private noise enters. Monet's fog paintings are visually similar in spirit: the global shape is still there, but the atmosphere/noise obscures the clean structure, so one has to look more carefully for the right signal. That feels close to "determinants are not enough; we need the worst-case direction."

**THE END**
{: .center}

<div class="divider"></div>

Now, it's time to take a break by appreciating the masterpiece of Monet.[^monet]


{: .center}
![Monet](../assets/post_images/fog.jpg){:height="400px" width="500px"}

**Houses of Parliament in the Fog**
{: .center}
_courtesy of https://high.org/collection/houses-of-parliament-in-the-fog/_
{: .center}
