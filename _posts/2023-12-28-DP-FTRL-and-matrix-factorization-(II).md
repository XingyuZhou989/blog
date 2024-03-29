---
comments: true
title: DP-FTRL and matrix factorization mechanism (II)
updated: 2023-12-28 22:00
---
$$
\newcommand{\bA}{\mathbf{A}}
\newcommand{\bB}{\mathbf{B}}
\newcommand{\bC}{\mathbf{C}}

\newcommand{\bH}{\mathbf{H}}
\newcommand{\bI}{\mathbf{I}}
\newcommand{\bG}{\mathbf{G}}
\newcommand{\bZ}{\mathbf{Z}}
\newcommand{\Real}{\mathbb{R}}
\newcommand{\cN}{\mathcal{N}}
$$

In this series of blog posts, I plan to write down some of my personal understanding (of course shaped by many wonderful papers) of DP-FTRL and matrix factorization mechanism.

<div class="divider"></div>

As motivated by Q1 in the end of the [previous post](https://xingyuzhou.org/blog/notes/DP-FTRL-and-matrix-factorization-(I))
, we would like to explore even better mechanisms than tree-based algorithm for the task of releasing private prefix sum in this post. The key idea here is to view the tree-based algorithm (as well as the other two simple noise-adding mechanisms mentioned in the beginning of [previous post](https://xingyuzhou.org/blog/notes/DP-FTRL-and-matrix-factorization-(I))) as special cases of a more general framework -- matrix factorization mechanism [[DMRSG22]](https://arxiv.org/pdf/2202.08312.pdf). In particular, one key contribution of [[DMRSG22]](https://arxiv.org/pdf/2202.08312.pdf) is to explicitly consider the stream nature and privacy under *adaptive* inputs, which is in contrast to previous offline settings (e.g.,[[LMHMR15]](https://people.cs.umass.edu/~miklau/assets/pubs/dp/Li15matrix.pdf), [[ENU20]](https://arxiv.org/pdf/1911.08339.pdf)). Following [[DMRSG22]](https://arxiv.org/pdf/2202.08312.pdf), let us have a quick understanding of the matrix factorization mechanism, with a focus on the task of computing prefix sum. Note that it can be easily generalized to general tasks (with replacement of the task matrix $$\bA$$ below).

The task of online releasing prefix sum over a sequence of gradients $$\bG = [g_1,\ldots, g_T]^{\top} \in \Real^{T \times d}$$ can be represented as $$\bA \bG$$ where $$\bA \in \Real^{T \times T}$$ is the all-ones lower-triangular matrix. To privatize $$\bA \bG$$, the matrix factorization approach first factorizes $$\bA = \bB \bC$$ and releases $$\bB (\bC \bG + \bZ)$$, where $$\bZ \in \Real^{T \times d}$$ is some proper privacy noise matrix. By post-processing of DP, it suffices to ensure that $$\bC \bG + \bZ$$ is private (although one needs to be careful to deal with adaptive inputs).
The high-level intuition behind this method is to adjust the space where one adds noise (hence $$\bC$$ is often called the *encoder*) and then reconstruct the required output using the *decoder* matrix $$\bB$$. The hope here is to achieve a better privacy and utility (e.g., total noise in noisy prefix sum) trade-off. As we will see later, roughly speaking, $$\bC$$ controls the variance for each added noise while $$\bB$$ controls the number of noise that will be added to each noisy prefix sum.

<div class="divider"></div>

Before searching for better mechanisms, let us first cast the tree-based algorithm and the other two mechanism to the matrix factorization framework with different choices of $$(\bB,\bC)$$. From this, we can see that indeed a careful choice of $$(\bB,\bC)$$ can improve the performance.

**Example 2.1 (Simple I mechanism as matrix factorization)** Simple I mechanism in [[CSS11]](https://eprint.iacr.org/2010/076.pdf) simply adds noise to each non-private prefix sum. This corresponds to $$\bB = \bI$$ and $$\bC = \bA$$. For privacy, the sensitivity is on the order of $$\sqrt{T}$$ in $$\ell_2$$ norm as changing the first element $$g_1$$ will impact all $$T$$ outputs in the space of $$\bC \bG = \bA \bG$$. Hence, each element in $$\bZ$$ is $$\cN(0,\sigma^2)$$ with $$\sigma^2 \approx O_{\delta}\left(\frac{T}{\epsilon^2}\right)$$ for $$(\epsilon,\delta)$$-DP.  For utility, each noisy prefix sum is simply the non-private prefix sum plus the $$t$$-th row vector $$z_t$$ in $$\bZ$$. Putting the two together, we have the total noise in the noisy prefix sum is $$O_{\delta}(T/\epsilon^2)$$ for $$(\epsilon,\delta)$$-DP.

**Example 2.2 (Simple II mechanism as matrix factorization)** Simple II mechanism in [[CSS11]](https://eprint.iacr.org/2010/076.pdf) simply adds noise to each input data point and then accumulates all noisy data points for noisy prefix sum. This corresponds to $$\bB = \bA$$ and $$\bC = \bI$$. For privacy, the sensitivity is on the order of $$O(1)$$ in $$\ell_2$$ norm as changing any element $$g_t$$ will at most change $$O(1)$$ in the output.  Hence, each element in $$\bZ$$ is $$\cN(0,\sigma^2)$$ with $$\sigma^2 \approx O_{\delta}\left(\frac{1}{\epsilon^2}\right)$$ for $$(\epsilon,\delta)$$-DP.  For utility, each noisy prefix sum needs to accumulate all previous noisy data points. Putting the two together, we have the total noise in the noisy prefix sum is again $$O_{\delta}(T/\epsilon^2)$$ for $$(\epsilon,\delta)$$-DP, the same as above.


**Example 2.3 (Tree-based algorithm as matrix factorization)**    The tree-based algorithm adds noise to the tree nodes in the complete binary tree as shown in [Fig.1.1](https://xingyuzhou.org/blog/notes/DP-FTRL-and-matrix-factorization-(I)#tree) of the previous post and then accumulates the corresponding noisy tree nodes for the final noisy prefix sum. This can also be viewed as a proper choice of $$\bB$$ and $$\bC$$. Let us first see a concrete toy example for the simple case when $$T = 4$$ and then give results for the general case later. In particular, for $$T = 4$$, we have

<a id="eq21"></a>
$$
\bC = \begin{pmatrix}
1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 \\
1 & 1 & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1 \\
0 & 0 & 1 & 1 \\
1 & 1 & 1 & 1
\end{pmatrix}, \quad
\bB = \begin{pmatrix}
1 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 1 & 0 & 0 & 0 & 0 \\
0 & 0 & 1 & 1 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 1
\end{pmatrix}.
\tag{2.1}
$$

To see it, as shown in the left sub-tree of [Fig.1.1](https://xingyuzhou.org/blog/notes/DP-FTRL-and-matrix-factorization-(I)#tree) of the previous post, the encoder matrix $$\bC \in \Real^{7 \times 4}$$ is responsible for computing the $$7$$ tree nodes (partial sums) using $$4$$ input data points. On the other hand, the decoder $$\bB \in \Real^{4 \times 7}$$ is responsible for computing the noisy prefix sum using the $$7$$ noisy tree nodes. One can check $$\bA = \bB \bC$$. For the general case, by the recursive nature of a binary tree, it is very natural to conjecture that $$\bC$$ would enjoy some recursive formula. Indeed, as shown in [[DMRSG22]](https://arxiv.org/pdf/2202.08312.pdf), if one defines the following recursive formula


$$
\bH_1 = \begin{pmatrix}
1
\end{pmatrix}, \quad
\bH_{k+1} = \begin{pmatrix}
\bH_k & \mathbf{0}  \\
0 & \bH_k \\
\mathbf{1} & \mathbf{1}
\end{pmatrix},
$$

then, for $$T =2^{k-1}$$, $$k \ge 1$$, $$\bC = \bH_k$$ and $$\bB$$ is a $$\{0,1\}$$-valued matrix such that $$\bB \bC = \bA$$ which can be found using tree structure (or bit representation of $$t$$) or using linear programming. As discussed in [Fig.1.1](https://xingyuzhou.org/blog/notes/DP-FTRL-and-matrix-factorization-(I)#tree) of the previous post, the total noise in noisy prefix sum under tree-based algorithm is only $$\tilde{O}_{\delta}(1/\epsilon^2)$$.

*Remark 2.1:* Note that due to the constraint of online streaming release, $$\bB$$ in the tree-based algorithm cannot be the one computed via $$\bA \bC^{\dagger}$$ where $$\bC^{\dagger}$$ is the Moore-Penrose pseudoinverse of matrix $$\bC$$. Interestingly, as shown in [[DMRSG22]](https://arxiv.org/pdf/2202.08312.pdf) (cf. Proposition C.1), $$\bA \bC^{\dagger}$$ is exactly the (non-streaming) Honaker fully efficient estimator in [[Hon15]](https://tpdp.journalprivacyconfidentiality.org/2015/abstracts/TPDP_2015_1.pdf). For general $$T=2^{k-1}$$, please refer to my [Python code](https://colab.research.google.com/drive/1B0mN9rQOZ-u8Ox9ZjNYkYpjBf187fcqL?usp=sharing) for computing $$\bB$$ for the tree-based algorithm. BTW, we can see that the all-zero columns in $$\bB$$ responds to the non-green nodes in [Fig.1.1](https://xingyuzhou.org/blog/notes/DP-FTRL-and-matrix-factorization-(I)#tree) of the previous post.

*Remark 2.2:* For approximate DP, Simple I and Simple II mechanisms have similar amounts of total noise as demonstrated above. However, if one considers pure DP with Laplace noise, one can even distinguish between the two mechanisms. In particular, for Simple I, the variance of the error in each prefix sum is one noise with variance of $$O(T^2/\epsilon^2)$$ (since now the sensitivity is in $$\ell_1$$ norm). On the other hand, for Simple II, the corresponding value is the sum of at  most $$O(T)$$ noise, each with variance of $$O(1/\epsilon^2)$$. This amounts to a total variance of $$O(T/\epsilon^2)$$, hence $$T$$ factor smaller.

<div class="divider"></div>

Thus, we have seen that there indeed exists the possibility of optimizing the choice of $$(\bB,\bC)$$ for a better privacy-utility trade-off in terms of total noise. In the next post, we will come up with a loss objective for the maximal total error in prefix sum so that we can use optimization to find the best $$(\bB,\bC)$$. But, we should also keep in mind, whether or not minimizing the maximal total noise in prefix sum leads to a tight final performance, as hinted in Q2 of the [previous post](https://xingyuzhou.org/blog/notes/DP-FTRL-and-matrix-factorization-(I)), which will be our later topic.




**THE END**
{: .center}

<div class="divider"></div>

Now, it's time to take a break by appreciating the masterpiece of Monet.


{: .center}
![Monet](../assets/post_images/water-lilies-2.jpg){:height="400px" width="500px"}

**Water Lilies**
{: .center}
_courtesy of https://www.wikiart.org/_
{: .center}
