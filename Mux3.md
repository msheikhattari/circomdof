```
template Mux3() {
    var i;
    signal input c[8];  // Constants \\s=8
    signal input s[3];   // Selector \\s=11
    signal output out; \\s=12

    component mux = MultiMux3(1); \\component

    for (i=0; i<8; i++) {
        mux.c[0][i] <== c[i]; \\c=8
    }

    for (i=0; i<3; i++) {
      s[i] ==> mux.s[i]; \\c=11
    }

    mux.out[0] ==> out; \\c=12
}
```
12 signals, 12 constraints. The dof of [[MultiMux3]] is 11.

`dof = 11`