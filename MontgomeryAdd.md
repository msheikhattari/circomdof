```
template MontgomeryAdd() {
    signal input in1[2]; // s = 2
    signal input in2[2]; // s = 4
    signal output out[2]; // s = 6

    var a = 168700;
    var d = 168696;

    var A = (2 * (a + d)) / (a - d);
    var B = 4 / (a - d);

    signal lamda; // s = 7

    lamda <-- (in2[1] - in1[1]) / (in2[0] - in1[0]);
    lamda * (in2[0] - in1[0]) === (in2[1] - in1[1]); // c = 1

    out[0] <== B*lamda*lamda - A - in1[0] -in2[0]; // c = 2
    out[1] <== lamda * (in1[0] - out[0]) - in1[1]; // c = 3
}
```
There are 7 signals and 3 constraints.

`dof = 4`