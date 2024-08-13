```
template Mux1() {
    var i;
    signal input c[2];  // Constants \\s=2
    signal input s;   // Selector \\s=3
    signal output out;  \\s=4

    component mux = MultiMux1(1); \\component

    for (i=0; i<2; i++) {
        mux.c[0][i] <== c[i]; 
    }
    \\c=2

    s ==> mux.s;\\c=3

    mux.out[0] ==> out; \\c=4
}
```
4 signals, 4 constraints. The dof of [[MultiMux1]] is 3 (called 1 time).

`dof = 3`