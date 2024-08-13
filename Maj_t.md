```
template Maj_t(n) {
    signal input a[n];
    signal input b[n];
    signal input c[n];
    signal output out[n];
    signal mid[n];
    \\s=5n

    for (var k=0; k<n; k++) {
        mid[k] <== b[k]*c[k];
        out[k] <== a[k] * (b[k]+c[k]-2*mid[k]) + mid[k];
    }
    \\c=2n
}
```
5n signals, 2n constraints.

`dof = 3n`