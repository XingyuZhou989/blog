---
comments: false
title: Strong convexity
updated: 2017-04-28 21:35
---

Strong convexity is one of the most important concepts in optimization, especially for guaranteeing a linear convergence rate of many gradient decent based algorithms. In this post, which is mainly based on my course project report, I would like to present some useful results on strong convexity. 

Let's us first begin with the definition. 

A differentiable function $$f$$ is strongly convex if 

$$
f(y)\ge f(x)+\nabla f(x)^T(y-x)+\frac{\mu}{2}||y-x||^2 \tag{1}\label{eq:def}
$$

for some $$\mu > 0$$ and all $$x, y$$. 

**Note:** Strong convexity doesn't necessarily require the function to be differentiable, and the gradient is replaced by the sub-gradient when the function is non-smooth.

Intuitively speaking, strong convexity means that there exists a quartic lower bound on the growth of the function. This directly implies that a strong convex function is strictly convex since the quartic lower bound growth is of course strictly grater than the linear growth. 

Although the definition in $$\eqref{eq:def}$$ is commonly used, it would be quite useful for us to note that there are several equivalent definitions for strong convexity.

### Equivalent Definitions of strong convexity
> **_Proposition_** _The following conditions are all equivalent to the condition that a differentiable function $$f$$ is strongly-convex with constant $$\mu > 0$$_.
$$ \begin{align}
		&f(y)\ge f(x)+\nabla f(x)^T(y-x)+\frac{\mu}{2}||y-x||^2,~\forall x, y.\\
		&g(x) = f(x)-\frac{\mu}{2}||x||^2~\text{is convex},~\forall x.\\
		&(\nabla f(x) - \nabla f(y)^T(x-y) \ge \mu ||x-y||^2,~\forall x, y.\\
		&f(\alpha x+ (1-\alpha) y) \le \alpha f(x) + (1-\alpha) f(y) - \frac{\alpha (1-\alpha)\mu}{2}||x-y||^2,\alpha \in [0,1].\\
	\end{align} $$



$$
\begin{equation*}
	\begin{split}
		F(x_{k+1}) &= f(x_{k+1}) + g(x_{k+1})\\
		& \mathop  \le \limits^{(a)} f(x_k) + \nabla f(x_k)^T(x_{k+1}-x_k) + \frac{L}{2}||x_{k+1} - x_k||^2 + g(x_{k+1})\\
		& = F(x_k) + \nabla f(x_k)^T(x_{k+1}-x_k) + \frac{L}{2}||x_{k+1} - x_k||^2 + g(x_{k+1}) - g(x_k)\\
		& \lep{b} F(x_k) - \frac{1}{2L}\mathcal{D}_g(x_k,L)\\
		& \lep{c} F(x_k) - \frac{\mu}{L}\left[ F(x_k) - F^*\right]
	\end{split}
\end{equation*}
$$
$$
