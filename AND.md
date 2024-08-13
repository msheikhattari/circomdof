```
template AND() {
    signal input a; \\s=1
    signal input b; \\s=2
    signal output out; \\s=3

    out <== a*b;\\c=1
}
```
3 signals, 1 constraint.

`dof = 2`