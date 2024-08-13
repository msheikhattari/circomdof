```
template Xor3(n) {
    signal input a[n]; \\s=n
    signal input b[n]; \\s=2n
    signal input c[n]; \\s=3n
    signal output out[n]; \\s=4n
    signal mid[n]; \\s=5n

    for (var k=0; k<n; k++) {
        mid[k] <== b[k]*c[k]; 
        out[k] <== a[k] * (1 -2*b[k]  -2*c[k] +4*mid[k]) + b[k] + c[k] -2*mid[k];
    }
    \\c=2n
}
```
5n signals and 2n constraints.

`dof = 3n`