```
template SMTHash1() {
    signal input key; \\s=1
    signal input value; \\s=2
    signal output out; \\s=3

    component h = MultiMiMC7(2, 91);   // Constant \\component
    h.in[0] <== key; \\c=1
    h.in[1] <== value; \\c=2
    h.k <== 1; \\c=3

    out <== h.out; \\c=4
}
```
3 signals, 4 constraints. The dof of [[MultiMiMC7]] is 3.

`dof = 2`