[Arithmetization I](https://medium.com/starkware/arithmetization-i-15c046390862)



> The first step in STARK is called *arithmetization*, and it is the translation (often referred to as ‘reduction’) of the problem of verifying a computation to the **problem of checking that a certain polynomial**, which can be evaluated efficiently on the verifier’s side (this is the ‘succinctly’ part), **is of low degree**.
>
> ...
>
> The verifier is convinced that the polynomial is of low degree if and only if the original computation is correct (except for an infinitesimally small probability).



Arithmetization : Computational Integrity Statement -> *Execution trace + polynomial constraints*



> The ability to express computational constraints as a *recurring pattern* of local constraints (i.e. **succinctness**), is fundamental to the verifier being *exponentially faster* than a naive replay of the computation.
>
> ---> allowing *succinct verification*



