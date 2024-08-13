```
template Ark(t, C, r) {
    signal input in[t]; \\s=t
    signal output out[t]; \\s=2t

    for (var i=0; i<t; i++) { \\c=t
        out[i] <== in[i] + C[i + r];
    }
}
```
2t signals, t constraints.

`dof = t`