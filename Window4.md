```
template Window4() {
    signal input in[4]; \\ s=4
    signal input base[2]; \\ s=6
    signal output out[2]; \\ s=8
    signal output out8[2];   // Returns 8*Base (To be linked) \\s=10

    component mux = MultiMux3(2); \\ component

    mux.s[0] <== in[0]; \\ c = 1
    mux.s[1] <== in[1]; \\ c = 2
    mux.s[2] <== in[2]; \\ c = 3

    component dbl2 = MontgomeryDouble(); \\ component
    component adr3 = MontgomeryAdd(); \\ component
    component adr4 = MontgomeryAdd(); \\ component
    component adr5 = MontgomeryAdd(); \\ component
    component adr6 = MontgomeryAdd(); \\ component
    component adr7 = MontgomeryAdd(); \\ component
    component adr8 = MontgomeryAdd(); \\ component

// in[0]  -> 1*BASE

    mux.c[0][0] <== base[0]; \\c = 4
    mux.c[1][0] <== base[1]; \\c = 5

// in[1] -> 2*BASE
    dbl2.in[0] <== base[0];
    dbl2.in[1] <== base[1];
    mux.c[0][1] <== dbl2.out[0];
    mux.c[1][1] <== dbl2.out[1];
    \\c = 9

// in[2] -> 3*BASE
    adr3.in1[0] <== base[0];
    adr3.in1[1] <== base[1];
    adr3.in2[0] <== dbl2.out[0];
    adr3.in2[1] <== dbl2.out[1];
    mux.c[0][2] <== adr3.out[0];
    mux.c[1][2] <== adr3.out[1];
    \\c = 15

// in[3] -> 4*BASE
    adr4.in1[0] <== base[0];
    adr4.in1[1] <== base[1];
    adr4.in2[0] <== adr3.out[0];
    adr4.in2[1] <== adr3.out[1];
    mux.c[0][3] <== adr4.out[0];
    mux.c[1][3] <== adr4.out[1];
    \\ c = 21

// in[4] -> 5*BASE
    adr5.in1[0] <== base[0];
    adr5.in1[1] <== base[1];
    adr5.in2[0] <== adr4.out[0];
    adr5.in2[1] <== adr4.out[1];
    mux.c[0][4] <== adr5.out[0];
    mux.c[1][4] <== adr5.out[1];
    \\ c=27

// in[5] -> 6*BASE
    adr6.in1[0] <== base[0];
    adr6.in1[1] <== base[1];
    adr6.in2[0] <== adr5.out[0];
    adr6.in2[1] <== adr5.out[1];
    mux.c[0][5] <== adr6.out[0];
    mux.c[1][5] <== adr6.out[1];
    \\ c=33

// in[6] -> 7*BASE
    adr7.in1[0] <== base[0];
    adr7.in1[1] <== base[1];
    adr7.in2[0] <== adr6.out[0];
    adr7.in2[1] <== adr6.out[1];
    mux.c[0][6] <== adr7.out[0];
    mux.c[1][6] <== adr7.out[1];
    \\c=39

// in[7] -> 8*BASE
    adr8.in1[0] <== base[0];
    adr8.in1[1] <== base[1];
    adr8.in2[0] <== adr7.out[0];
    adr8.in2[1] <== adr7.out[1];
    mux.c[0][7] <== adr8.out[0];
    mux.c[1][7] <== adr8.out[1];
    \\c=45

    out8[0] <== adr8.out[0];
    out8[1] <== adr8.out[1];
    \\c=47

    out[0] <== mux.out[0];
    out[1] <== - mux.out[1]*2*in[3] + mux.out[1];  // Negate y if in[3] is one
    \\c=49
}
```
10 signals and 49 constraints. The dof of [[MultiMux3]] is 19 (called 1 time). The dof of[[MontgomeryDouble]] is 2 (called 1 time). The dof of [[MontgomeryAdd]] is 4 (called 6 times). In total: 10 - 49 + 19 + 2 + 24 = 6

`dof = 6`