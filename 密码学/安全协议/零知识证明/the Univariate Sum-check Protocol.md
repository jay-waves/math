### a ReedSolomn encoded IOP for Univariate Sum-check

**Input：** $\mathbb{F}, L, H, k, \mu$，其中 $\sum_{a\in H}\hat{f}(a)=\mu$

1. $\mathcal{P}$ 唯一分解 $\hat{f}$ 为 $\hat{f}=x\cdot \hat{g}(x)+\zeta+\hat{Z}_{H}(x)\hat{h}(x)$，其中， $deg(\hat{g})<\vert H\vert -1$, $\zeta$ 是常数，$deg(\hat{h})<k+1-\vert H\vert$。
2. $\mathcal{P}$ 发送谕示 $\hat{h}\vert_{L}\in RS\left[ L, \frac{k+1-\vert H\vert}{\vert L\vert } \right]$ 给 $\mathcal{V}$。[^1]
3. $\mathcal{V}$ 接受，当且仅当 $\hat{p}\vert_{L}\in RS\left[ L, \frac{\vert H\vert - 1}{\vert L \vert} \right]$；其中 $$\hat{p}(x)=\frac{\vert H\vert \cdot \hat{f}(x)-\mu-\vert H\vert \cdot \hat{Z}_{H}(x)\hat{h}(x)}{x}$$ 否则，$\mathcal{V}$ 拒绝。

**引理 1：** 上述 RS-encoded IOP 具有完美完备性和完美可靠性。

证明：

*完美完备性（Perfect Completeness）：* [^2]

完美完备性依赖于以下引理，

**引理 2：** 令 $H$ 是域 $\mathbb{F}$ 的乘法子群，令 $\hat{g}$ 为域 $\mathbb{F}$ 上的单变量多项式，其度严格小于 $\vert H\vert$。$\sum_{a\in H}\hat{g}(a)=\vert H\vert \cdot \hat{g}(0)$。

由 $\hat{g}, \hat{h}$ 的定义，以及**引理2**，有：
$$\mu=\sum_{a\in H}\{a\cdot \hat{g}(a)+\zeta+\hat{Z}_{H}(a)\hat{h}(a)\}=\sum_{a\in H}\{a\cdot \hat{g}(a)\}+\vert H\vert \zeta + 0=\vert H\vert \zeta$$

因此, 有 $\zeta = \frac{\mu}{\vert H\vert}$. 结合 $\hat{p}$ 的定义, 可以得出:

$$\hat{p}(x)=\frac{\vert H\vert \cdot \hat{f}(x)-\mu -\vert H\vert \cdot \hat{Z}_{H}(x)\hat{h}(x)}{x}=\vert H\vert \cdot\hat{g}(x)$$

因此, 有 $\hat{p}\vert_{L}\in RS\left[ L, \frac{\vert H\vert - 1}{\vert L \vert} \right]$

*完美可靠性（Perfect Soundness）：* [^3]

使用假设反正法。有一个假断言 $\mu$，即 $\sum_{a\in H}\hat{f}(a)=\mu'\neq \mu$，我们可以证明：对于满足 $deg(\hat{h})<k+1-\vert H\vert$ 的所有 $\hat{h}$，总有 $deg(\hat{p})\geq \vert H\vert-1$。

如果满足 $deg(\hat{p})\lt \vert H\vert -1$ 的 $\hat{p}$ 存在, 那么由**引理2**, 有 $\sum_{a\in H}a\cdot \hat{p}(a)=0\cdot \hat{p}(0)\cdot\vert H\vert=0$.

代入 $\hat{p}$ 的定义可得，$$\sum_{a\in H}a\cdot\hat{p}(a)=\vert H\vert \sum_{a\in H}\hat{f}(a)-\vert H\vert\cdot \mu=\vert H\vert \cdot(\mu'-\mu)\neq 0$$
这是矛盾的，所以假设不成立。因此得证。


[^1]: $u\vert_H=(u(a_1),u(a_2),...,u(a_{H}))\in \mathbb{F}^{\vert H\vert}$
[^2]: completeness，完备性：真断言总能被验证为真
[^3]: soundness，可靠性：假断言很小可能被验证为真