```
template Ch_t(n) {
    signal input a[n]; \\s=n
    signal input b[n]; \\s=2n
    signal input c[n]; \\s=3n
    signal output out[n]; \\s=4n

    for (var k=0; k<n; k++) {
        out[k] <== a[k] * (b[k]-c[k]) + c[k]; \\c=n
    }
}
```
4n signals, n constraints.

`dof = 3n`