```
template Sigma() {
    signal input in; \\s=1
    signal output out; \\s=2

    signal in2; \\s=3
    signal in4; \\s=4

    in2 <== in*in; \\c=1
    in4 <== in2*in2; \\c=2

    out <== in4*in; \\c=3
}
```
4 signals, 3 constraints.

`dof = 1`