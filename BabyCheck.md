```
template BabyCheck() {
    signal input x; // s=1
    signal input y; // s=2

    signal x2; // s=3
    signal y2; // s=4

    var a = 168700;
    var d = 168696;

    x2 <== x*x; // c=1
    y2 <== y*y; // c=2

    a*x2 + y2 === 1 + d*x2*y2; // c=3
}
```
4 signals and 3 constraints.

`dof = 1`