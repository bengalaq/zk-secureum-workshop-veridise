# Day 2 Quiz

Please refer to the files under `src` for all circuits mentioned in
the questions. Unless specified otherwise, select all applicable
answers to the following questions.

To answer some of the questions you will need to render the CDGs of
the circuits under `src` using your SaaS account.

> ## Question 1 (1 point)
>
> Which of the following statements hold for a CDG:
>
> 1. [ ] A CDG encodes constraint and data-flow relations between input, intermediate, and output signals of a circuit.
> 2. [ ] An under-constrained output signal does not have any incident constraint edges.
> 3. [ ] An under-constrained output signal, `O`, may have incident constraint edges but none of `O`'s neighbors is an input signal.
> 4. [ ] An under-constrained output signal, `O`, may have incident constraint edges but there is no constraint path between `O` and any input signal.

**ANSWER**: 1, 4

(2) An output signal is considered under-constrained if not constrained by an input signal or by a signal constant value. Ergo, an output signal may be constrained by intermediate signals or by a function of itself (e.g., `outp * (outp - 1) === 0`, which allows `outp` to be either `0` or `1`) and still be under-constrained.

(3) An output signal can be properly constrained by a constant value (e.g., `outp === 0`).

> ## Question 2 (1 point)
>
> Consider the following circom statement: `a <== b + c`. Which of following are true for its CDG:
>
> 1. [ ] The CDG will only have two constraints edges: one between `a` and `b` and one between `a` and `c`.
> 2. [ ] The CDG will only have two data-flow edges: one between `a` and `b` and one between `a` and `c`.
> 3. [ ] The CDG will not have any constraint edges.
> 4. [ ] The CDG will not have any data-flow edges.
> 5. [ ] Every pair of signals will be connected by either a constraint edge or a data-flow edge but not both.
> 6. [ ] None of the above.

**ANSWER**: 6

The CDG will have constraint edges `a <> b`, `a <> c`, and `b <> c`; and data-flow edges `c to a` and `b to a`.
You can generate the CDG via SaaS to visualize the solution.

> ## Question 3 (1 point)
>
> Consider the following circom statement: `a <-- b + c`. Which of following are true for its CDG:
>
> 1. [ ] The CDG will only have two constraints edges: one between `a` and `b` and one between `a` and `c`.
> 2. [ ] The CDG will only have two data-flow edges: one between `a` and `b` and one between `a` and `c`.
> 3. [ ] The CDG will not have any constraint edges.
> 4. [ ] The CDG will not have any data-flow edges.
> 5. [ ] Every pair of signals will be connected by either a constraint edge or a data-flow edge but not both.

**ANSWER**: 2, 3

The CDG will only have data-flow edges `a to c` and `b to c`, since `<--` assignment only creates data-flow assignments and does not generate any constraints.
You can generate the CDG via SaaS to visualize the solution.

> ## Question 4 (2 points)
>
> Consider template `div` in `div_v1.circom` and its CDG.
>
> 4a (1 point). Does the circuit contain a division by zero bug?
>
> 1. [ ] Yes
> 2. [ ] No

**ANSWER**: 1

This is the text book definition of the pattern; nothing prevents `x2` from being `0`.

> 4b (1 point). Does the circuit contain any unconstrained signals (inputs or outputs)?
>
> 1. [ ] Yes
> 2. [ ] No

**ANSWER**: 2

All three signals are constrained by `o * x2 === x1`, so all signals are constrained.

> ## Question 5 (5 points)
>
> Consider template `div` in `div_v2.circom` and its CDG.
>
> 5a (1 point). Does the circuit contain a division by zero bug?
>
> 1. [ ] Yes
> 2. [ ] No

**ANSWER**: 2

No, because of the constraint on isZero.out (line 23).

> 5b (1 point). Will an analysis over the CDG report a division by zero bug for template `div`?
>
> 1. [ ] Yes
> 2. [ ] No

**ANSWER**: 1

Yes, this will be a spurious warning (i.e., a false positive).

> 5c (3 points). If you pay close attention to circuit `IsZero` in `div_v2.circom`, you might notice that it contains non-deterministic witness generation (you can also confirm that through the CDG).
> However, as the comment in `div_v2.circom` indicates, this circuit is borrowed from the official circom library.
> So, is the library really buggy or is there some sort of magic happening?

**ANSWER**: The circuit is correct because `out` can be set to zero if and only if `in` has an inverse, which is equivalent to `in` not being zero.

> ## Question 6 (8 points)
>
> Now let's turn our attention to the circuits in `range-check.circom`.
> Do you notice anything suspicious with the CDG?
> If so, can you come up with inputs for the main component that would be an unpleasant surprise to the developer of the circuit?
> Please submit your answer in JSON format suitable for witness generation.

**ANSWER**: There will be several suspicious things. The actual bug is the missing range check on `in[0]`.

The following input file will generate a witness whose output signal will be set to 1.
`
{
"in": ["21888242871839275222246405745257275088548364400416034343698204186575808495616", "1"]
}
`

This will lead to a proof that states 21888242871839275222246405745257275088548364400416034343698204186575808495616 < 1, which is of course bogus.
