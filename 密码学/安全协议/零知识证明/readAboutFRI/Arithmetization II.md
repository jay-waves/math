[Arithmetization II](https://medium.com/starkware/arithmetization-ii-403c3b3f4355)



problem reduction flow:

Computation Integrity

<===> *Execution trace* satisfies *polynomial constraints*

<===>  **one** polynomial is guaranteed to be of *low degree*



role of *domain*

role of *error correction codes*   ( ===> introducing *Reed-Solomon codes*)



> ...extend the execution trace by thinking of it as an evaluation of a polynomial on some domain (*interpolating*), and *evaluating* this same polynomial *on a much larger domain*.



____

==(Intimidation warning)==

procedure of translation (trying to extract and absrtact the examples):



Assume the *depth of execution trace* is $n$, denoted by $A_i,0\le i< n$.

And the number of  *polynomial constraints* is $m$ (no matter with $n$), the $j$-th constraint is denoted by $F_j(A_k,\ldots,A_{k+t_j})=0$ for some $k$. To be specific,  $F_j$ indicates the recurrence relation within (some part of) execution trace, and $d_j$ indicates the "order" involved in the recurrence, and say "some $k$" is $0\le s_j\le k\le s_j+d_j-1<n-t_j$, and $d_j$ tends to be *pretty large*.

(Example: CI Statement for *Fibonacci* sequence
The recurrence relation polynomail is $F_3=F_3(A_k,A_{k+1},A_{k+2})$ where $t_j=t_3=2$, and "some $k$" means $0\le k < 510$ because the sequence ends at 511th, in this case, $s_j=s_3=0,d_j=d_3=510$.)



Pick out one Large field (like $\mathbb{Z}_p$ where $p$ is a prime largely bigger than $n$), denoted by $\mathbb{F}$, and $\mathbb{F}^*$ denotes its multiplicative group, which has a subgroup $G$ with order $|G|=n$ (means that $n$ is a factor of $|\mathbb{F}^*|$), assume $g$ is some generator of $G$.



Denote $A_i$ by $g$: say there is a ploynomial $f:G\rightarrow\mathbb{F}$ (over $\mathbb{F}$) whose degree is less than $n$, such that $f(g^i)=A_i$ for $0\le i < n$.

Denote constraints by $f$: substitute the $A_i$ with $f(g^i)$, we can get the $j$-th constraint: $F_j(A_k,\ldots,A_{k+t_j})=F_j(f(g^k),\ldots,f(g^{k+t_j}))=0\;$.

(Note that if some $F_j=0$ can be degraded to first-order linear equation (like $f(g^{k_j})+b_j=0$), we call it as *boundary constraints*.)



Then, we can extract a new *single univariate polynomial* from the equation above, and can be denoted by $f$, that is $F_j(f(x),\ldots,f(g^{t_j}x))$, which has $d_j$ roots: $g^{s_j},\ldots,g^{s_j+d_j-1}$. In more detail, its degree is more than $d_j$ and less than $\text{deg}F_j\times\text{deg}f<n$.

(notice that $F_j(f(x),\ldots,f(g^{t_j}x))$ is still $G\rightarrow\mathbb{F}$)



For simplification, let's omit the $j$, and assume $s_j=0$ (means the recurrence relation starts from 0, which is often the case.) Then let's define another polynomial:
$$
p(x)=\dfrac{F(f(x),\ldots,f(g^tx))}{\prod_{i=0}^{d-1}(x-g^i)}
$$
$p$ is called as the *composition polynomial*, which degree is less than $n-d$.



**The fact is, the more the original CI is correct, the closer $p$ is to a low-degree.** On the one hand, if the original CI is indeed correct, then the degree of  $p$ should be less than $n-d$; on the other hand, if there exists one error in the original execution trace, that means there exists $A_e=g^{e}\in\{1,g,\ldots,g^{d-1}\}\;$such that:
$$
F(f(g^e),\ldots,f(g^{e+t}))=F(A_e,\ldots,A_{e+t})\neq0
$$
this can deduce that $p$ is equal to some structure like  $\dfrac{q(x)}{x-g^e}$, and we have:
$$
\text{def}\;p=\text{deg}\;q\times\text{deg}(x-g^e)^{-1}\ge\text{deg}{(x-g^e)^{-1}}=n-1\ge n-d
$$
thus the **original CI is correct if and only if $p$ is of low degree**.



To make the evaluation of $p$ more succinct, we can transform $p$ into:

$$
p(x)=\dfrac{F(f(x),\ldots,f(g^tx))}{\prod_{i=0}^{d-1}(x-g^i)}=\dfrac{\prod_{i=n-d}^n(x-g^i)\cdot F(f(x),\ldots,f(g^tx))}{x^n-1}
$$
by noticing that $x^n-1=x^{|G|}-1=\prod_{g\in G}(x-g)$.

(computing $\prod_{i=0}^{d-1}(x-g^i)$ is linear in $|G|$, while computing $x^{|G|}-1$ is logarithmic in $|G|$. )





____

