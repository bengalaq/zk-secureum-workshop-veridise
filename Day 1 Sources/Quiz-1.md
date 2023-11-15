# Day 1 Quiz

Please refer to the files under `src` for all circuits mentioned in
the questions. Unless specified otherwise, select all applicable
answers to the following questions.

> ## Question 1 (1 point)
>
>  Which of the following statements are true for zk-SNARKS.
> 1. The verifier can infer the secret inputs given in a proof.
> 2. zk-SNARKS can be used to introduce privacy in a DeFi application.
> 3. zk-SNARKS can only scale privacy preserving DeFi applications.
> 4. The prover does not need any input from the verifier.
> 5. The verifier has higher algorithmic complexity than the prover.
> 6. The verifier has lower algorithmic complexity than the prover.

**ANSWER**: 2,4,6

(1) This contradicts the zk aspect of zk-SNARKS.

(3) SNARKs can be used to scale any application (e.g., see zk-EVM; there isn't anything private in this application)

(5) Recall that the S(uccinct) in SNARK means that the verifier will do less work than the prover.

> ## Question 2 (1 point)
>
> A zk circuit written in circom...:
>
> 1. only encodes the constraints of the verifier.
> 2. only implements the logic of the witness generation.
> 3. encodes both the constraints of the verifier and the witness generation logic.
> 4. can directly generate zk proofs.
> 5. can only contain constraints that are at most quadratic.

**ANSWER**: 3,5

(1,2) Circom circuits encode both constraints and witness generation.

(4) A circom circuit creates a witness generator and constraints; you need to use a proving framework like snarkjs to generate a proof from the generated witness.

> ## Question 3 (2 point)
>
> Assume we have a main component instantiated as `IsOdd(1)`. Which of the
> following sets will entirely satisfy the constraints of the verifier (i.e.,
> will satisfy the constraints of the verifier for all possible values in the set):
>
>
> 1. `(in, out) s.t. 0 <= in < p, 0 <= out < p`, where `p` is the default prime used by circom.
> 2. `(in, out) s.t. 0 <= in < p, 0 <= out < 2`, where `p` is the default prime used by circom.
> 3. `(in, out) s.t. 0 <= in < 2, 0 <= out < 2`.
> 4. `(in, out) s.t. 0 <= in < 2, 0 <= out < p`, where `p` is the default prime used by circom.
> 5.  None of the above.

**ANSWER**: 3,4

(1,2) Signal `in` must be binary (i.e., `0 <= in < 2`) because of the constraints introduced by `Num2Bits`.

> ## Question 4 (4 points)
>
> Assume we have a main component instantiated as `IsOdd(n)` where `n != 1`.
>
> **4a (2 points).** Which of the following sets will entirely satisfy the constraints of the verifier (i.e.,
> will satisfy the constraints of the verifier for all possible values in the set):
>
> 1. `(in, out) s.t. 0 <= in < p, 0 <= out < p`, where `p` is the default prime used by circom.
> 2. `(in, out) s.t. 0 <= in < p, 0 <= out < 2`, where `p` is the default prime used by circom.
> 3. `(in, out) s.t. 0 <= in < 2^n, 0 <= out < 2`.
> 4. `(in, out) s.t. 0 <= in < 2^n, 0 <= out < p`, where `p` is the default prime used by circom.
> 5.  None of the above.

**ANSWER**: 3

(1) Signal `in` must fit in `n` bits (`Num2Bits`) and signal `out` must be binary (constraint on line 38).

(2) Signal `in` must fit in `n` bits (`Num2Bits`).

(4) Signal `out` must be binary (constraint on line 38).

> **4b (2 points).** Given a single value of signal `in`, how many distinct and valid
> proofs can be constructed for the verifier of the circuit?
>
> 1.  0
> 2.  1
> 3.  2
> 4.  `p`, where `p` is the default prime used by circom.

**ANSWER**: 3

When `n != 1`, signal `out` only has two possible values `{0, 1}`. So, given a value of `in` we can only construct two proofs.

> ## Question 5 (5 point)
>
> Which of the following hold for **all** instantiations of template `FibCircuit`:
>
> 1.  The witness generation always succeeds.
> 2.  The generated constraint system will be satisfied when signal `out` is equal to the `n`-th Fibonacci number, where `n` is the value of the template parameter.
> 3.  The generated constraint system is only satisfied by a single value of signal `out`.

**ANSWER**: 3

1. Constraint generation fails when `n == 1` (give it a try).
2. The addition on line 20 overflows when `n >= 367`. The 367th Fibonacci number is larger than the default circom prime.
