[STARK Math](https://medium.com/starkware/stark-math-the-journey-begins-51bd2b063c71)



> *Computational Integrity* (CI) is a fundamental property that underlies commerce.



> *Inclusive Accountability*: a node with a standard computational setup (a web-connected laptop) should be able to verify the integrity of all transactions in the system.
>
> The prevalent method to verify CI in permissionless blockchains is **via naive replay**: all nodes are asked to re-execute (replay) the computations that verify each and every transaction.
>
> 
>
> two drawbacks:
>
> - lack **Privacy**
> - lack **Scalability**



> *Interactive Proof* model
>
> ...
>
> two properties holds if the proof system *solves* CI (when updating from state $\mathcal{A}$ to state $\mathcal{B}$) :
>
> - **Completeness**
> - **Soundness** (with tiny false-positive probability, can be set to an acceptable level like $1/(2^{128})$)

