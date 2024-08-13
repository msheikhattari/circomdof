```
template Num2BitsNeg(n) {
    signal input in; \\ s = 1
    signal output out[n]; \\ s= n + 1
    var lc1=0;

    component isZero; \\component

    isZero = IsZero(); 

    var neg = n == 0 ? 0 : 2**n - in;

    for (var i = 0; i<n; i++) {
        out[i] <-- (neg >> i) & 1;
        out[i] * (out[i] -1 ) === 0; \\ c = n
        lc1 += out[i] * 2**i;
    }

    in ==> isZero.in; \\ c = n + 1



    lc1 + isZero.out * 2**n === 2**n - in; \\ c = n + 2
}
```
There are n + 1 signals and n + 2 constraints. The dof of [[IsZero]] is 1. 

`dof = 0`