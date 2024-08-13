```
template EscalarMulWindow(base, k) {

    signal input in[2]; \\s=2
    signal input sel[4]; \\s=6
    signal output out[2]; \\s=8

    var table[16][2];
    component mux; \\component
    component adder; \\component

    var i;

    table = EscalarMulW4Table(base, k); \\component
    mux = MultiMux4(2); \\component
    adder = BabyAdd(); \\component

    for (i=0; i<4; i++) {
        sel[i] ==> mux.s[i]; \\c=4
    }

    for (i=0; i<16; i++) {
        mux.c[0][i] <== table[i][0];
        mux.c[1][i] <== table[i][1];
    }\\c=36

    in[0] ==> adder.x1; \\c=37
    in[1] ==> adder.y1; \\c=38

    mux.out[0] ==> adder.x2; \\c=39 
    mux.out[1] ==> adder.y2; \\c=40

    adder.xout ==> out[0]; \\c=41
    adder.yout ==> out[1]; \\c=42
}
```
8 signals, 42 constraints. The dof of [[MultiMux4]] is 36 (called 1 time). The dof of [[BabyAdd]] is 4 (called 1 time).

`dof = 6`

