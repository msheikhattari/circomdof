```
template Num2Bits(n) {
    signal input in; //s=1
    signal output out[n]; //s=n+1
    var lc1=0;

    var e2=1;
    for (var i = 0; i<n; i++) { // c = n*1 = n
        out[i] <-- (in >> i) & 1;
        out[i] * (out[i] -1 ) === 0;
        lc1 += out[i] * e2;
        e2 = e2+e2;
    }

    lc1 === in; // c = n+1
}
```
n+1 signals and n+1 constraints. 

`dof = 0`.