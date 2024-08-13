```
template ShR(n, r) {
    signal input in[n]; \\s=n
    signal output out[n]; \\s=2n

    for (var i=0; i<n; i++) {
        if (i+r >= n) {
            out[i] <== 0; 
        } else {
            out[i] <== in[ i+r ];
        }
    }
    \\c=n
}
```
2n signals, n constraints.

`dof = n`