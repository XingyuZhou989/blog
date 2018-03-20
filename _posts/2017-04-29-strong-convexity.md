---
comments: true
title: Strong convexity
updated: 2017-04-29 21:35
---

Strong convexity is one of the most important concepts in optimization, especially for guaranteeing a linear convergence rate of many gradient decent based algorithms. In this post, which is mainly based on my course project report, I would like to present some useful results on strong convexity. 

Let's us first begin with the definition. 

A differentiable function $$f$$ is strongly convex if 

$$
f(y)\ge f(x)+\nabla f(x)^T(y-x)+\frac{\mu}{2}\lVert y-x \rVert^2 \tag{1}\label{eq:def}
$$

for some $$\mu > 0$$ and all $$x, y$$. 

**Note:** Strong convexity doesn't necessarily require the function to be differentiable, and the gradient is replaced by the sub-gradient when the function is non-smooth.

Intuitively speaking, strong convexity means that there exists a quartic lower bound on the growth of the function. This directly implies that a strong convex function is strictly convex since the quartic lower bound growth is of course strictly grater than the linear growth. 

Although the definition in $$\eqref{eq:def}$$ is commonly used, it would be quite useful for us to note that there are several equivalent conditions for strong convexity.

<div class="divider"></div>

### Equivalent Conditions of Strong Convexity
The following proposition gives equivalent conditions for strong convexity. The key insight behind this result and its proof is that we can relate a strongly-convex function ($$e.g., f(x)$$) to another convex function ($$e.g., g(x)$$), which enables us to apply the equivalent conditions for a convex function to obtain the result.

> **Proposition** _The following conditions are all equivalent to the condition that a differentiable function $$f$$ is strongly-convex with constant $$\mu > 0$$_.
$$ \begin{align}
		&(i)~f(y)\ge f(x)+\nabla f(x)^T(y-x)+\frac{\mu}{2}\lVert y-x \rVert^2,~\forall x, y.\\
		&(ii)~g(x) = f(x)-\frac{\mu}{2}\lVert x \rVert^2~\text{is convex},~\forall x.\\
		&(iii)~(\nabla f(x) - \nabla f(y)^T(x-y) \ge \mu \lVert x-y\rVert^2,~\forall x, y.\\
		&(iv)~f(\alpha x+ (1-\alpha) y) \le \alpha f(x) + (1-\alpha) f(y) - \frac{\alpha (1-\alpha)\mu}{2}\Vert x-y\rVert^2,~\alpha \in [0,1].\\
	\end{align} $$

_Proof:_ 

$$(i) \equiv (ii):$$ It follows from the first-order condition for convexity of $$g(x)$$, i.e., $$g(x)$$ is convex if and only if $$g(y)\ge g(x) + \nabla g(x)^T(y-x),~\forall x,y.$$

$$(ii) \equiv (iii):$$ It follows from the monotone gradient condition for convexity of $$g(x)$$, i.e., $$g(x)$$ is convex if and only if $$(\nabla g(x) - \nabla g(y))^T(x-y) \ge 0,~\forall x,y.$$ 

$$(ii) \equiv (iv):$$ It simply follows from the definition of convexity, i.e., $$g(x)$$ is convex if $$g(\alpha x+ (1-\alpha) y) \le \alpha g(x) + (1-\alpha) g(y), ~\forall x,y, \alpha\in [0,1].$$ $$\tag*{$\Box$}$$

**Note:** The equivalence still holds for non-smooth function with gradient replaced by sub-gradient. For a proof, please refer to Lemma 1 of the [note](https://arxiv.org/pdf/1803.06573.pdf).

<div class="divider"></div>

### Implications of Strong Convexity
There are also some other conditions that are implied by strong convexity while are not equivalent to strong convexity, which means that these conditions are more general than strong convexity. 

> **Proposition** _For a continuously differentiable function $$f$$, the following conditions are all implied by strong convexity (SC) condition_.
$$ \begin{align}
		&(a)~\frac{1}{2}\lVert\nabla f(x)\rVert^2\ge \mu (f(x)-f^*),~\forall x.\\
		&(b)~\lVert\nabla f(x) - \nabla f(y)\rVert \ge \mu \lVert x-y\rVert~\forall x,y.\\
		&(c)~f(y)\le f(x)+\nabla f(x)^T(y-x)+\frac{1}{2\mu}\lVert\nabla f(y)- \nabla f(x)\rVert^2,~\forall x,y.\\
		&(d)~(\nabla f(x) - \nabla f(y)^T(x-y) \le \frac{1}{\mu} \lVert\nabla f(x)-\nabla f(y)\rVert^2, ~\forall x,y.\\
	\end{align} $$

**Note:** The condition (a) is often called Polyak-Lojasiewicz (PL) inequality which is quite useful for establishing linear convergence for a lot of algorithms.

_Proof:_

SC $$\rightarrow$$ (a): Taking minimization respect to $$y$$ on both sides of $$\eqref{eq:def}$$ yields

$$
f(x^*) \ge f(x)-\frac{1}{2\mu}||\nabla f(x)||^2
$$

Re-arranging it we have the PL-inequality.

SC $$\rightarrow$$ (b): Using Cauchy-Schwartz on the equivalent condition (iii) in the last Proposition, we have 

$$
\lVert\nabla f(x) - \nabla f(y)\rVert \lVert x-y \rVert \ge (\nabla f(x) - \nabla f(y)^T(x-y) \ge \mu \lVert x-y\rVert^2 
$$ 

and dividing both sides by $$\lVert x-y \rVert$$ (assuming $$x \neq y$$) gives (a) (while (a) clearly holds if $$x = y$$).


SC $$\rightarrow$$ (c): Let us consider the function $$\phi_x(z) = f(z) - \nabla f(x)^T z$$. It is easy to see that $$\phi_x(z)$$ is strongly-convex with the same $$\mu$$ since 

$$
(\nabla \phi_x(z_1) - \nabla \phi_x(z_2)^T(z_1-z_2) = (\nabla f(z_1) - \nabla f(z_2)^T(z_1-z_2) \ge \mu \lVert z_1-z_2 \rVert ^2
$$

where we again use the equivalent condition (iii) in the last Proposition for strong convexity. 

Then, the condition (c) can be directly obtained by applying the PL-inequality to the strongly-convex function $$\phi_x(z)$$ with $$z^* = x$$

$$
\begin{align}
(f(y) - \nabla f(x)^T y) - (f(x) - \nabla f(x)^T x) = \phi_x(y) - \phi_x(x) &\le \frac{1}{2\mu}\nabla ||\phi_x(y)||^2 \\
&= \frac{1}{2\mu}||\nabla f(y) - \nabla f(x)||^2
\end{align}
$$

SC $$\rightarrow$$ (d): Interchanging $$x$$ and $$y$$ in condition (c) yields,

$$
f(x)\le f(y)+\nabla f(y)^T(x-y)+\frac{1}{2\mu}\lVert \nabla f(x)- \nabla f(y)\rVert^2 \tag{2}\label{eq:interchange}
$$

Condition (d) is obtained by adding $$\eqref{eq:interchange}$$ and condition (c). $$\tag*{$\Box$}$$

<div class="divider"></div>

### Application

We have already seen the application of condition (iii) in proving useful results in the last section. In this section, let us look at one simple application of condition (iv) for strong convexity.

>**Proposition** _Let $$h = f + g$$ where $$f$$ is strongly convex function and $$g$$ is a convex function, then $$h$$ is strongly convex function_

_Proof:_

$$
\begin{align}
	h(\alpha x + (1-\alpha)y) &= f(\alpha x + (1-\alpha)y) + g(\alpha x + (1-\alpha)y)\\
	& \le \alpha f(x) + (1-\alpha)f(y) - \frac{\mu \alpha (1-\alpha)}{2}||x-y||^2 + g(\alpha x + (1-\alpha)y)\\
	& \le \alpha h(x) + (1-\alpha) h(y) - \frac{\mu \alpha (1-\alpha)}{2}||x-y||^2 
\end{align}
$$

where the first inequality follows from the fact that $$f$$ is strongly convex; the second inequality holds since $$g$$ is convex. $$\tag*{$\Box$}$$

**Note:** Although the proof is simple, it has its own importance. For example, any L2-regularized problem of the form $$h(x) = f(x)+ \lambda \lVert x \rVert^2$$, where $$f$$ is convex and $$\lambda >0$$, is strongly convex. 



**THE END**
{: .center}

<div class="divider"></div>

Now, it's time to take a break by appreciating the masterpiece of Monet.


{: .center}
![Monet](http://xingyuzhou.org/blog/assets/post_images/impression-sunrise.jpg){:height="400px" width="500px"}

**Impression Sunrise**
{: .center}
_courtesy of www.Claude-Monet.com_
{: .center}



