```
template BabyAdd() {
    signal input x1; // s=1
    signal input y1; // s=2
    signal input x2; // s=3
    signal input y2; // s=4
    signal output xout; // s=5
    signal output yout; // s=6

    signal beta; // s=7
    signal gamma; // s=8
    signal delta; // s=9
    signal tau; // s=10

    var a = 168700;
    var d = 168696;

    beta <== x1*y2; // c=1
    gamma <== y1*x2; // c=2
    delta <== (-a*x1+y1)*(x2 + y2); // c=3
    tau <== beta * gamma; // c=4

    xout <-- (beta + gamma) / (1+ d*tau);
    (1+ d*tau) * xout === (beta + gamma); // c=5

    yout <-- (delta + a*beta - gamma) / (1-d*tau);
    (1-d*tau)*yout === (delta + a*beta - gamma); // c=6
}
```
10 signals and 6 constraints.

`dof = 4`