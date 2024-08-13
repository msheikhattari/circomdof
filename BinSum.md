```
template BinSum(n, ops) {
    var nout = nbits((2**n -1)*ops); // let x = floor(log_2((2**n - 1)*ops))
    signal input in[ops][n]; \\ s = n*ops
    signal output out[nout]; \\ s = n*ops + x

    var lin = 0;
    var lout = 0;

    var k;
    var j;

    var e2;

    e2 = 1;
    for (k=0; k<n; k++) {
        for (j=0; j<ops; j++) {
            lin += in[j][k] * e2;
        }
        e2 = e2 + e2;
    }

    e2 = 1;
    for (k=0; k<nout; k++) {
        out[k] <-- (lin >> k) & 1;

        // Ensure out is binary
        out[k] * (out[k] - 1) === 0; // c = x

        lout += out[k] * e2;

        e2 = e2+e2;
    }

    // Ensure the sum;

    lin === lout; \\ c = x + 1
}

```
Define $x \equiv$ `floor(log_2((2**n - 1)*ops))`. There are `n*ops + x` signals and `x + 1` constraints.

`dof = n*ops - 1`