```
template MultiMux1(n) {
    signal input c[n][2];  // Constants \\s=2n
    signal input s;   // Selector \\s=2n + 1
    signal output out[n]; \\s=3n+1

    for (var i=0; i<n; i++) {

        out[i] <== (c[i][1] - c[i][0])*s + c[i][0]; \\c=n

    }
}
```
3n+1 signals, n constraints.

`dof = 2n+1`