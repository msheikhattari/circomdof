```
template SigmaPlus() {
    signal input in2[32]; \\s=32
    signal input in7[32]; \\s=64
    signal input in15[32]; \\s=96
    signal input in16[32]; \\s=128
    signal output out[32]; \\s=160
    var k;

    component sigma1 = SmallSigma(17,19,10); \\component
    component sigma0 = SmallSigma(7, 18, 3); \\component
    for (k=0; k<32; k++) {
        sigma1.in[k] <== in2[k]; 
        sigma0.in[k] <== in15[k];
    }
    \\c=64

    component sum = BinSum(32, 4); \\component
    for (k=0; k<32; k++) {
        sum.in[0][k] <== sigma1.out[k];
        sum.in[1][k] <== in7[k];
        sum.in[2][k] <== sigma0.out[k];
        sum.in[3][k] <== in16[k];
    }
    \\c=192

    for (k=0; k<32; k++) {
        out[k] <== sum.out[k];
    }
    \\c=224
}
```
160 signals, 224 constraints. The dof of [[SmallSigma]] is 32 (called 2 times). The dof of [[BinSum]] is 127 (called 1 time). 

`dof = 127`