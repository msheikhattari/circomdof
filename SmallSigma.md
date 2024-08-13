```
template SmallSigma(ra, rb, rc) {
    signal input in[32]; \\s=32
    signal output out[32]; \\s=64
    var k;

    component rota = RotR(32, ra); \\component
    component rotb = RotR(32, rb); \\component
    component shrc = ShR(32, rc); \\component

    for (k=0; k<32; k++) {
        rota.in[k] <== in[k];
        rotb.in[k] <== in[k];
        shrc.in[k] <== in[k];
    }
    \\c=96

    component xor3 = Xor3(32); \\component
    for (k=0; k<32; k++) {
        xor3.a[k] <== rota.out[k];
        xor3.b[k] <== rotb.out[k];
        xor3.c[k] <== shrc.out[k];
    }
    \\c=192

    for (k=0; k<32; k++) {
        out[k] <== xor3.out[k];
    }
    \\c=224
}
```
64 signals and 224 constraints. The dof of [[RotR]] is 32 (called 2 times). The dof of [[ShR]] is 32 (called 1 time). The dof of [[XOR3]] is 96 (called 1 time). 

`dof = 32`