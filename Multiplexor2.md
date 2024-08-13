```
template Multiplexor2() {
    signal input sel; \\s=1
    signal input in[2][2]; \\s=5
    signal output out[2]; \\s=7

    out[0] <== (in[1][0] - in[0][0])*sel + in[0][0]; \\c=1
    out[1] <== (in[1][1] - in[0][1])*sel + in[0][1]; \\c=2
}
```
7 signals and 2 constraints.

`dof = 5`