```
template BigSigma(ra, rb, rc) {
    signal input in[32]; \\s=32
    signal output out[32]; \\s=64
    var k;

    component rota = RotR(32, ra); \\component
    component rotb = RotR(32, rb); \\component
    component rotc = RotR(32, rc); \\component
    for (k=0; k<32; k++) {
        rota.in[k] <== in[k];
        rotb.in[k] <== in[k];
        rotc.in[k] <== in[k];
    }
    \\c=3*32 = 96

    component xor3 = Xor3(32); \\component

    for (k=0; k<32; k++) {
        xor3.a[k] <== rota.out[k];
        xor3.b[k] <== rotb.out[k];
        xor3.c[k] <== rotc.out[k];
    }
    \\c = 6*32 = 192

    for (k=0; k<32; k++) {
        out[k] <== xor3.out[k];
    }
    \\c= 192 + 32 = 224
}
```
64 signals, 224 constraints. The dof of [[RotR]] is 32 (called 3 times). The dof of [[Xor3]] is 96 (called 1 time).

`dof = 32`