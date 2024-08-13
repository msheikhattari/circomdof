```
template Main() {
    signal input a; \\s=1
    signal input b; \\s=2
    signal output out; \\s=3

    component sha256_2 = Sha256_2(); \\component

    sha256_2.a <== a; \\c=1
    sha256_2.b <== a; \\c=2
    out <== sha256_2.out; \\c=3
}
```
3 signals, 3 constraints. The dof of [[Sha256_2]] is -312.

`dof = -312`