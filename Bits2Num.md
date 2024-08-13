```
template Bits2Num(n) {
    signal input in[n]; \\ s = n
    signal output out; \\ s = n + 1
    var lc1=0;

    var e2 = 1;
    for (var i = 0; i<n; i++) {
        lc1 += in[i] * e2;
        e2 = e2 + e2;
    }

    lc1 ==> out; \\ c = 1
}
```
n + 1 signals and 1 constraint.

`dof = n`