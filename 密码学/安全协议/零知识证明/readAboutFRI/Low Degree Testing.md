[Low Degree Testing](https://medium.com/starkware/low-degree-testing-f7614f5172db)



similiar to FFT, only $O(\text{log}\;d)$ queries needed to determine degree of a polynomial is lower than $d$.



naive way (direct way):

query $d+1$ values on $L$, to determine a polynomial $f:L\rightarrow\mathbb{F}$ is of low degree less than $d$.

1. *fixed* location $z_1,\ldots,z_d$.
2. *random* location $w$.



FRI way:

 two methodology:

- **Having the number of queries for the case of two polynomials.**

  Assume testing $f$ and $g$ for low-degree $d$, other than seperately testing, we test:
  $$
  h(x)=f(x)+\alpha g(x)\;\;\text{where }\alpha\text{ is randomly picked in $\mathbb{F}$}.
  $$
  (reduce the $2d+1$ queries to $d+1$ )

  (need the prover's help: to compute the $h(z)=f(z)+\alpha g(x)$)

- **Splitting a polynomial into two smaller-degree polynomials.**

  Just notice that every polynomial $f$ with $d$ degree can be splitted into:
  $$
  f(x)=g(x^2)+xh(x^2)\;\;\text{where }g,h\text{ is of degree less than }d/2
  $$
  