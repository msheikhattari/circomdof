```
template RotR(n, r) {
    signal input in[n]; \\s=n
    signal output out[n]; \\s=2n

    for (var i=0; i<n; i++) {
        out[i] <== in[ (i+r)%n ]; \\c=n
    }
}
```
2n signals, n constraints.

`dof = n`