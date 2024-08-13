```
template BinSub(n) {
    signal input in[2][n]; \\ s = 2n
    signal output out[n]; \\ s = 3n

    signal aux; \\ s = 3n + 1

    var lin = 2**n;
    var lout = 0;

    var i;

    for (i=0; i<n; i++) {
        lin = lin + in[0][i]*(2**i);
        lin = lin - in[1][i]*(2**i);
    }

    for (i=0; i<n; i++) {
        out[i] <-- (lin >> i) & 1;

        // Ensure out is binary
        out[i] * (out[i] - 1) === 0; \\ c = n

        lout = lout + out[i]*(2**i);
    }

    aux <-- (lin >> n) & 1;
    aux*(aux-1) === 0; \\ c = n + 1
    lout = lout + aux*(2**n);

    // Ensure the sum;
    lin === lout; \\ c = n + 2
}
```
3n+1 signals and n+2 constraints.

`dof = 2n - 1`