```
template MontgomeryDouble() {
    signal input in[2]; // s = 2
    signal output out[2]; // s = 4

    var a = 168700;
    var d = 168696;

    var A = (2 * (a + d)) / (a - d);
    var B = 4 / (a - d);

    signal lamda; // s = 5
    signal x1_2; // s = 6

    x1_2 <== in[0] * in[0]; // c = 1

    lamda <-- (3*x1_2 + 2*A*in[0] + 1 ) / (2*B*in[1]);
    lamda * (2*B*in[1]) === (3*x1_2 + 2*A*in[0] + 1 ); // c = 2

    out[0] <== B*lamda*lamda - A - 2*in[0]; // c = 3
    out[1] <== lamda * (in[0] - out[0]) - in[1]; // c = 4
}
```
There are 6 signals and 4 constraints. 

`dof = 2`