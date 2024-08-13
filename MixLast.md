```
template MixLast(t, M, s) {
    signal input in[t]; \\s=t
    signal output out; \\s=t+1

    var lc = 0;
    for (var j=0; j<t; j++) {
        lc += M[j][s]*in[j];
    }
    out <== lc; \\c=1
}
```
t + 1 signals, 1 constraint.

`dof = t`