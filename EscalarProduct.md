```
template EscalarProduct(w) {
    signal input in1[w]; \\s=w
    signal input in2[w]; \\s=2w
    signal output out; \\s=2w + 1
    signal aux[w]; \\s=3w+1
    var lc = 0;
    for (var i=0; i<w; i++) {
        aux[i] <== in1[i]*in2[i]; \\c=w
        lc = lc + aux[i];
    }
    out <== lc; \\c=w+1
```
3w + 1 signals, w + 1 constraints.

`dof = 2w`