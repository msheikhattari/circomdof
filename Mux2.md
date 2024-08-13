```
template Mux2() {
    var i;
    signal input c[4];  // Constants \\s=4
    signal input s[2];   // Selector \\s=6
    signal output out; \\s=7

    component mux = MultiMux2(1); \\component

    for (i=0; i<4; i++) {
        mux.c[0][i] <== c[i]; \\c=4
    }

    for (i=0; i<2; i++) {
      s[i] ==> mux.s[i];\\c=6
    }

    mux.out[0] ==> out;\\c=7
}
```
7 signals, 7 constraints. The dof of [[MultiMux2]] is 6.

`dof = 6`