```
template Edwards2Montgomery() {
    signal input in[2]; // s = 2
    signal output out[2]; // s = 4

    out[0] <-- (1 + in[1]) / (1 - in[1]);
    out[1] <-- out[0] / in[0];


    out[0] * (1-in[1]) === (1 + in[1]); // c = 1
    out[1] * in[0] === out[0]; // c = 2
}
```
There are 4 signals and 2 constraints.

`dof = 2`.