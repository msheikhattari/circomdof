```
template SMTHash2() {
    signal input L; \\s=1
    signal input R; \\s=2
    signal output out; \\s=3

    component h = MultiMiMC7(2, 91);   // Constant \\component
    h.in[0] <== L; \\c=1
    h.in[1] <== R; \\c=2
    h.k <== 0; \\c=3

    out <== h.out; \\c=4
}
```
3 signals, 4 constraints. The dof of [[MultiMiMC7]] is 3.

`dof = 2`