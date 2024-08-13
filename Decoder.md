```
template Decoder(w) {
    signal input inp; \\s=1
    signal output out[w]; \\s=1 + w
    signal output success; \\s = 2 + w
    var lc=0;

    for (var i=0; i<w; i++) {
        out[i] <-- (inp == i) ? 1 : 0;
        out[i] * (inp-i) === 0; \\c=w
        lc = lc + out[i];
    }

    lc ==> success; \\c=1+w
    success * (success -1) === 0; \\c=2+w
}
```
2 + w signals, 2 + w constraints.

`dof = 0`