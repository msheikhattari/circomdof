```
template Mix(t, M) {
    signal input in[t]; \\s=t
    signal output out[t]; \\s=2t

    var lc;
    for (var i=0; i<t; i++) {
        lc = 0;
        for (var j=0; j<t; j++) {
            lc += M[j][i]*in[j];
        }
        out[i] <== lc; \\c=t
    }
}
```
2t signals, t constraints.

`dof = t`