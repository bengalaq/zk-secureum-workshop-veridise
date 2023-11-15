# Day 4 Quiz
​
Please refer to the files under `src/` for all circuits mentioned in
the questions. Unless specified otherwise, select all applicable
answers to the following questions.
​
> ## Question 1 (1 point)
> Which property is necessary and sufficient for a circuit to be under-constrained?
> 1. It contains an arithmetic overflow/underflow bug.
> 2. For each input, there are multiple distinct outputs that satisfy the constraints.
> 3. For every possible value of the output signals, there is a value for the input and intermediate signals that satisfies the constraints.
> 4. For every possible value of the input signals, there is a value for the output and intermediate signals that satisfies the constraints.
> 5. None of the above.
​
**ANSWER**: 5
​
Below are explanations for why (1-4) are incorrect:
​
(1) Consider constraint $out = in_1 + in_2$ where $in_1$ and $in_2$ are input signals. Then $out$ is a deterministic function of $in_1$ and $in_2$ even though there is an overflow.
​
(2) This is a sufficient but not necessary condition. To be under-constrained, a circuit just needs one input for which multiple distinct outputs satisfy the constraints.
​
(3) Consider a constraint system of the form $in = out*out \land in*(in-1) = 0$. This is set of constraints is under-constrained since when $in = 1$ both $out = 1$ and $out = p-1$ satisfy the constraints. However, when $out = 5$, there is no possible value for the input which satisfies the constraints.
​
(4) Take the counterexample to (3). Here, when $in = 5$, there is no possible value of the output signal that satisfies the constraints, as the constraint $in*(in-1)$ forces $in$ to be 1 or 0.
​
> ## Question 2 (1 point)
> We say that a set of constraints $C$ is *sound* with respect to a deterministic witness generating program $P$ if every variable assignment that satisfies $C$ corresponds to an execution trace in $P$. Likewise we say a set of constraints $C$ is *complete* with respect to $P$ if every trace of $P$ satisfies $C$. Now suppose for a program $P$, a developer writes a set of constraints $C$ which happen to be under-constrained. Then:
>
> 1. [ ] $C$ is not sound with respect to $P$.
> 2. [ ] $C$ is not complete with respect to $P$.
> 3. [ ] $C$ is sound with respect to $P$.
> 4. [ ] $C$ is complete with respect to $P$.
> 5. [ ] $C$ could be sound with respect to $P$.
> 6. [ ] $C$ could be complete with respect to $P$.
​
**ANSWER**: 1, 6
​
To see why (1) is true, note that *soundness* implies that $C$ is not under-constrained. (6) is true because $C$ could be equivalent to True (i.e, 1=1) and it would be under-constrained and nevertheless a complete circuit, since all execution traces of $P$ trivially satisify the constraint in $C$ (i.e., 1 = 1 is true for any execution trace of $P$).
​
Below are explanations for why 2-5 are incorrect:
​
(2) Since (6) is correct (2) is not correct.
​
(3) Since (1) is correct (3) is incorrect.
​
(4) $C$ may be under-constrained for some inputs but over-constrained on others.
​
(5) Ruled out since (1) is correct.
​
​
> ## Question 3 (1 point)
> Suppose the Circom prime was equal to 53. Then in `bits.circom`, which of the following statements are true about `Bits(n)`?
>
> 1. [ ] If we add the constraints $out[i]*(out[i] - 1) = 0$ for each $i$ then $out[0]$ is deterministic.
> 2. [ ] If we add the constraints $out[i]*(out[i] - 1) = 0$ for each $i$ then each $out[i]$ is deterministic for all $0 < n < p$.
> 3. [ ] If we add the constraints $out[i]*(out[i] - 1) = 0$ for each $i$ and $n < 7$ then the circuit is not under-constrained.
> 4. [ ] If we add the constraints $out[i]*(out[i] - 1) = 0$ for each $i$ and $n < 6$ then the circuit is not under-constrained.
​
**ANSWER**: 4
​
Here is a counterexample for (1-3):
​
Let $n = 6$.
​
Witness 1:  $in = 9$, $out[0] = 1$, $out[1] = 0$, $out[2] = 0$, $out[3] = 1$, $out[4] = 0$, $out[5] = 0$.
​
Witness 2:  $in = 9$, $out[0] = 0$, $out[1] = 1$, $out[2] = 1$, $out[3] = 1$, $out[4] = 1$, $out[5] = 1$.
​
Essentially this counterexample is demonstrating that if $2^n > p$ then you can express $in$ as a bit array in two distinct ways. Witness 1 is the expected bit representation, and witness 2 is the bit representation of a value $x$ (62 in this case) such that $p \leq x < 2^n$ and $x % p = in$. (4) is correct because when $n < 6$ then $2^n < 53$.
​
​
> ## Question 4 (2 points)
>
> Answer the following questions:
>
> 4a (1 point). In `npointtransform.circom`, the `nPointTransforms` circuit is known to be under-constrained. Find two witnesses that assign the same values to the input signals but different values to the output signals.
​
**ANSWER**:
You can run Picus to get the following counterexample:
```
input:
    main.in[1][0]: 1
    main.in[1][1]: 0
    main.in[2][0]: 1
    main.in[2][1]: 0
    main.in[3][0]: 1
    main.in[3][1]: 1
    main.in[4][0]: 1
    main.in[4][1]: 0
first possible outputs:
    main.out[0][0]: 0
    main.out[0][1]: 0
    main.out[1][0]: 1
    main.out[1][1]:
    main.out[2][0]: 1
    main.out[2][1]: 1
    main.out[3][0]: 1
    main.out[3][1]: 1
    main.out[4][0]: 1
    main.out[4][1]: 1
  second possible outputs:
    main.out[0][0]: 0
    main.out[0][1]: 1
    main.out[1][0]: 1
    main.out[1][1]: 1
    main.out[2][0]: 1
    main.out[2][1]: 1
    main.out[3][0]: 1
    main.out[3][1]: 1
    main.out[4][0]: 1
    main.out[4][1]: 1
```
​
> 4b (1 point). Fix the circuit so that it is no longer under-constrained.
​
**ANSWER**: Add a constraint in the loop to make sure $in[i][0] \neq 0$.
​
> ## Question 5 (8 points)
> Answer the following questions:
>
> 5a (4 points). In the original `unary.circom`, with a main component instantiated as `UnaryEnc(3)`, is the circuit under-constrained?
>     - If it is, respond "Yes" along with an explanation for why it is under-constrained and upload a fix to `unary.circom`
>       that makes the circuit properly constrained. The fix should only impose additional constraints without modifying the existing ones.
>     - Otherwise, respond "No" along with an explanation for why it is properly constrained.
​
**ANSWER**: Yes. For example, with $inp = 0$, we have:
​
```
  first possible outputs:
    main.out[0]: 0
    main.out[1]: 0
    main.out[2]: 0
    main.success: 1
  second possible outputs:
    main.out[0]: 1
    main.out[1]: 1
    main.out[2]: 1
    main.success: 0
```
​
One possible fix is the following:
​
```
pragma circom 2.0.7;
​
include "libs/comparators.circom";
​
/*
 * INPUT: inp
 * OUTPUT: out[w]. out[i] = 1 if inp > i and 0 otherwise.
 * OUTPUT: success. Equals 1 if and only if the number of 1s in out is equal to inp
 * NOTE: If inp is larger than w, out[i] should equal 1 forall 0 <= i < w.
 *       Intuitively, this is the only case where inp can't be encoded with the unary encoding.
 */
template UnaryEnc(w) {
    signal input inp;
    signal output out[w];
    signal output success;
    var lc=0;
​
    for (var i=0; i<w; i++) {
        out[i] <-- (inp > i) ? 1 : 0;
        /* out[i] is either 0 or 1 */
        out[i] * (out[i] - 1) === 0;
        lc = lc + out[i];
    }
    for (var i=w-1; i>0; i--) {
        /* either out[i] is 0, or out[i] and out[i - 1] are both 1 */
        out[i] * (out[i - 1] + out[i] - 2) === 0;
    }
​
    component ie = IsEqual();
    ie.in[0] <== lc;
    ie.in[1] <== inp;
    ie.out ==> success;
    /* either success is 1, or out[w - 1] is 1 (or both) */
    (success - 1) * (out[w - 1] - 1) === 0;
​
    /* Added constraints */
    /* If inp > w, we already constrained success to 0 */
    /* The remaining underconstrained case is inp <= w, where we need to constrain */
    /* success to 1 */
    var logw = 0;
    var v = 1;
    while (v <= w) {
      v = v + v;
      logw = logw + 1;
    }
    component let = LessEqThan(logw);
    let.in[0] <== inp;
    let.in[1] <== w;
    let.out === success;
}
​
component main = UnaryEnc(3);
```
Essentially, the fix makes sure that if `in <= w` then `success` must be 1. This fix takes advantage of the fact even though the circuit is under-constrained, the distinct output values will have different `success` values. In the correct encoding, `success = 1` but in the incorrect encoding `success = 0`.
​
> 5b (4 points). In the original `unary.circom`, with a main component instantiated as `UnaryEncCaller(3)`, is the circuit under-constrained?
> - If it is, respond "Yes" along with an explanation for why it is under-constrained and upload a fix to `unary.circom` that makes the circuit properly constrained. The fix should only impose additional constraints without modifying the existing ones.
> - Otherwise, respond "No" along with an explanation for why it is properly constrained and upload `unary.circom` unchanged.
​
**ANSWER**: No. The `UnaryEncCaller` circuit is not under-constrained because it adds the constraint `enc.success === 1` to eliminate the incorrect encoding.
