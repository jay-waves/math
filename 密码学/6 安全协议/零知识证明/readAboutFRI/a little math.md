$$
\text{ord}(a^k) = \dfrac{ord(a)}{\text{gcd}(ord(a), k)}\in\{t\;|\;\text{ord}(a)=k\cdot t,k\in\mathbb{Z}\}\\
\text{where }a \text{ could be some generator of multiplicative group }G\text{, thus ord}(a)=|G|\\
\text{Thus, it can be said:}\\
\text{if }|G|\text{ has a facor t}\;(|G|=kt)\text{, then there exists one subgroup }H\text{ that:}\\
H\le G, |H| = t\;\big|\; |G|,\text{ and }a^t\text{ is the generator of }H.
$$











$n=\prod_{i=1}^m p_i^{\omega_i}$

$\varphi(n)=\prod_{i=1}^m(p_i^{\omega_i}-p_i^{\omega_i-1})=n\cdot\prod_{i=1}^m(1-\dfrac{1}{p_i})$

$\dfrac{\varphi(n)}n{=\prod_{i=1}^m(1-\dfrac{1}{p_i})}$

$\lim_{n\rightarrow\infty}\sum_{i=1}^n\dfrac{1}{i}=\lim_{k\rightarrow\infty}\prod_{p \text{ is prime}}(1+\dfrac{1}{p}+...+\dfrac{1}{p^k})\\=\prod_{p<\infty,\;p \text{ is prime}}(\dfrac{1}{1-\frac{1}{p}})\\=\prod_{p<\infty,\;p \text{ is prime}}(\dfrac{p}{p-1})$
