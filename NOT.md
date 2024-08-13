```
template NOT() {
    signal input in; \\s=1
    signal output out; \\s=2

    out <== 1 + in - 2*in; \\c=1
}

```
2 signals, 1 constraint.

`dof = 1`