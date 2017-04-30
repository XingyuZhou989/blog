---
comments: true
title: Lipschitz continuous gradient
updated: 2017-04-30 14:35
---

Last time, we talked about [strong convexity](http://xingyuzhou.org/blog/notes/strong-convexity). Today, let us look at another important concept in convex optimization, named _Lipschitz continuous gradient_ condition, which is essential to ensuring convergence of many gradient decent based algorithms. The post is also mainly based on my course project report.  

It is worth noting that there exits a duality (Fenchel duality) between strong convexity and Lipschitz continuous gradient, which implies that once we have a good understanding of one, we may easily understand the other one. 

**Note:** Indeed, all the results in this post can be easily proved via the same method adopted in the post of strong convexity. This is the beauty of duality!

As usual, let's us first begin with the definition. 

A differentiable function $$f$$ is said to have an L-Lipschitz continuous gradient if for some $$L>0$$

$$
\lVert \nabla f(x) - \nabla f{y}\rVert \le L \lVert x-y\rVert,~\forall x,y.
$$

**Note:** The definition doesn't assume convexity of $$f$$. 

Now, we will list some other conditions that are related or equivalent to Lipschitz continuous gradient condition. 

$$
\begin{align}
	[0]~&\lVert\nabla f(x) - \nabla f(y)\rVert \le L \lVert x-y\rVert,~\forall x,y.\\
	[1]~&g(x) = \frac{L}{2}x^T x - f(x) \text{ is convex },~\forall x\\
	[2]~&f(y)\le f(x)+\nabla f(x)^T(y-x)+\frac{L}{2}\lVert y-x\rVert^2,~\forall x,y.\\
	[3]~&(\nabla f(x) - \nabla f(y)^T(x-y) \le L \rVert x-y\rVert^2, ~\forall x,y.\\
	[4]~&f(\alpha x+ (1-\alpha) y) \ge \alpha f(x) + (1-\alpha) f(y) - \frac{\alpha (1-\alpha)L}{2}\lVert x-y\rVert^2,~\forall x,y \text{ and } \alpha \in [0,1]\\
	[5]~&f(y)\ge f(x)+\nabla f(x)^T(y-x)+\frac{1}{2L}\lVert\nabla f(y)-\nabla f(x)\rVert^2,~\forall x,y.\\
	[6]~&(\nabla f(x) - \nabla f(y)^T(x-y) \ge \frac{1}{L} \lVert \nabla f(x)-\nabla f(y)\rVert^2, ~\forall x,y.\\
	[7]~&f(\alpha x+ (1-\alpha) y) \le \alpha f(x) + (1-\alpha) f(y) - \frac{\alpha (1-\alpha)}{2L}\lVert\nabla f(x)-\nabla f(y)\rVert^2,~\forall x,y \text{ and }\alpha \in [0,1].
\end{align}
$$

**Note:** We assume that the domain for $$f$$ and $$g$$ are both $$\mathbb{R}^n$$, and hence convex.

<div class="divider"></div>

### Relationships Between Conditions

The next proposition gives the relationships between all the conditions mentioned above. If you have already mastered all the tricks in the post of [strong convexity](http://xingyuzhou.org/blog/notes/strong-convexity), you can easily prove all the results by yourself. Try it now!

>**Proposition** _For a function $$f$$ with a Lipschitz continuous gradient over $$\mathbb{R}^n$$, the following implications hold:_
>
$$
[5] \equiv [7] \rightarrow [6] \rightarrow [0] \rightarrow [1] \equiv [2] \equiv [3] \equiv [4]
$$
>
If we further assume that $$f$$ is convex, then we have all the conditions $$[0]-[7]$$ are equivalent.

_Proof:_ Again, the key idea behind the proof is transformation, i.e., transform a $$f$$ with Lipschitz continuous gradient to another convex function $$g$$, which enables us to apply the equivalent conditions for convexity.

$$[1] \equiv [2]$$: It follows from the first-order condition for convexity of $$g(x)$$, i.e., $$g(x)$$ is convex if and only if $$g(y)\ge g(x) + \nabla g(x)^T(y-x),~\forall x,y.$$

$$[1] \equiv [3]$$: It follows from the monotone gradient condition for convexity of $$g(x)$$, i.e., $$g(x)$$ is convex if and only if $$(\nabla g(x) - \nabla g(y))^T(x-y) \ge 0,~\forall x,y.$$

$$[1] \equiv [4]$$: It simply follows from the definition of convexity, i.e., $$g(x)$$ is convex if $$g(\alpha x+ (1-\alpha) y) \le \alpha g(x) + (1-\alpha) g(y), ~\forall x,y, \alpha\in [0,1].$$

$$[0]\rightarrow[3]$$: It simply follows from the Cauchy-Schwartz inequality.

$$[6]\rightarrow[0]$$: It simply follows from the Cauchy-Schwartz inequality.

$$[7]\rightarrow[5]$$: Interchanging $$x$$ and $$y$$ in [7] and re-arranging, we have 

$$
\begin{align}
	f(y) \ge f(x) + \frac{f(x+\alpha (y-x)) -f(x)}{\alpha} + \frac{1-\alpha}{2L}\lVert\nabla f(x) - \nabla f(y)\rVert^2
\end{align}
$$

As $$\alpha \downarrow 0$$, we get $$[5]$$.

$$[5]\rightarrow[7]$$: Let $$z = \alpha x + (1-\alpha) y \in \mathbb{R}^n$$, we have 

$$
	f(x)\ge f(z)+\nabla f(z)^T(x-z)+\frac{1}{2L}\lVert \nabla f(x)-\nabla f(z)\rVert^2
$$

$$
	f(y)\ge f(z)+\nabla f(z)^T(y-z)+\frac{1}{2L}||\nabla f(y)-\nabla f(z)||^2
$$

Multiplying the first inequality with $$\alpha$$ and second inequality with $$1-\alpha$$, and adding them together yields

$$
\begin{align}
	f(\alpha x+ (1-\alpha) y) &\le \alpha f(x) + (1-\alpha) f(y) - \frac{\alpha}{2L}||\nabla f(x)-\nabla f(z)||^2 - \frac{1-\alpha}{2L}||\nabla f(y)-\nabla f(z)||^2\\
	& \le \alpha f(x) + (1-\alpha) f(y) - \frac{\alpha (1-\alpha)}{2L}||\nabla f(x)-\nabla f(y)||^2
\end{align}
$$

where the second inequality follows from the inequality $$\alpha \lVert x\rVert^2 + (1-\alpha) \lVert y\rVert^2 \ge \alpha (1-\alpha)\lVert x-y\rVert^2.$$

If $$f$$ is convex, we can easily show $$[1] \rightarrow [5]$$, which implies that all the conditions are equivalent in this case.

$$[1]\rightarrow[5]$$: Let us consider the function $$\phi_x(z) = f(z) - \nabla f(x)^T z$$, which obtain its optimum at $$z^* = x$$ as $$f$$ is convex. Moreover, we have $$h(z) = \frac{L}{2}z^Tz - \phi_x(z)$$ is convex since $$[1]$$ holds, which implies that 

$$
\phi_x(z)\le \phi_x(y)+\nabla \phi_x(y)^T(z-y)+\frac{L}{2}\lVert z-y\rVert^2
$$
Taking minimization with respect to $$z$$ on both sides, yields,

$$
\begin{align}
	f(y) - f(x) - \nabla f(x) (y-x) &= \phi_x(y) - \phi_x(x)\\
	& \ge \frac{1}{2L}\lVert \nabla \phi_x(y)\rVert^2\\
	& = \frac{1}{2L}\lVert \nabla f(y) - \nabla f(x)\rVert^2
\end{align}
$$

Re-arranging gives the result. $$\tag*{$\Box$}$$


**THE END**
{: .center}

<div class="divider"></div>

Now, it's time to take a break by appreciating the masterpiece of Monet.


{: .center}
![Monet](http://xingyuzhou.org/blog/assets/post_images/garden-at-sainte-adresse.jpg){:height="400px" width="500px"}

**The Garden at Sainte-Adresse**
{: .center}
_courtesy of www.Claude-Monet.com_
{: .center}



