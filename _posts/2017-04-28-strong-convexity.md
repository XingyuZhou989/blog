---
title: Strong convexity
updated: 2017-04-28 21:35
---

Strong convexity is one of the most important concepts in optimization, especially for guaranteeing a linear convergence rate of many gradient decent based algorithms. In this post, which is mainly based on my course project report, I would like to present some useful results on strong convexity. 

A differentiable function $$f$$ is strongly convex if 

$$
f(y)\ge f(x)+\nabla f(x)^T(y-x)+\frac{\mu}{2}||y-x||^2 \label{eq:def}
$$

for some $$\mu > 0$$. The $$\eqref{eq:def}$$ is the 
<!-- $$
\begin{equation*}
	\begin{split}
		F(x_{k+1}) &= f(x_{k+1}) + g(x_{k+1})\\
		& \mathop  \le \limits^{(a)} f(x_k) + \nabla f(x_k)^T(x_{k+1}-x_k) + \frac{L}{2}||x_{k+1} - x_k||^2 + g(x_{k+1})\\
		& = F(x_k) + \nabla f(x_k)^T(x_{k+1}-x_k) + \frac{L}{2}||x_{k+1} - x_k||^2 + g(x_{k+1}) - g(x_k)\\
		& \lep{b} F(x_k) - \frac{1}{2L}\mathcal{D}_g(x_k,L)\\
		& \lep{c} F(x_k) - \frac{\mu}{L}\left[ F(x_k) - F^*\right]
	\end{split}
\end{equation*}
$$ -->
