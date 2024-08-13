```
template MixS(t, S, r) {
    signal input in[t]; \\s=t
    signal output out[t]; \\s=2t


    var lc = 0;
    for (var i=0; i<t; i++) {
        lc += S[(t*2-1)*r+i]*in[i];
    }
    out[0] <== lc; \\c=1
    for (var i=1; i<t; i++) {
        out[i] <== in[i] +  in[0] * S[(t*2-1)*r + t + i -1];
    }
    \\c=t
}
```
2t signals, t constraints.

`dof = t`