```
template Mux4() {
    var i;
    signal input c[16];  // Constants \\s=16
    signal input s[4];   // Selector \\s=20
    signal output out; \\s=21

    component mux = MultiMux4(1); \\component

    for (i=0; i<16; i++) {
        mux.c[0][i] <== c[i]; \\c=16
    }

    for (i=0; i<4; i++) {
      s[i] ==> mux.s[i]; \\c=20
    }

    mux.out[0] ==> out; \\c=21
}
```
21 signals, 21 constraints. The dof of [[MultiMux4]] is 20. 

`dof = 20`