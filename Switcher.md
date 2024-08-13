```
template Switcher() {
    signal input sel;
    signal input L;
    signal input R;
    signal output outL;
    signal output outR;
    signal aux;
    \\s=6

    aux <== (R-L)*sel;    // We create aux in order to have only one multiplication
    outL <==  aux + L;
    outR <== -aux + R;
    \\c=3
}
```
6 signals, 3 constraints.

`dof = 3`