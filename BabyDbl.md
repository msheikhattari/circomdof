```
template BabyDbl() {
    signal input x; // s=1
    signal input y; // s=2
    signal output xout; // s=3
    signal output yout; // s=4

    component adder = BabyAdd(); //component
    adder.x1 <== x; // c=1
    adder.y1 <== y; // c=2
    adder.x2 <== x; // c=3
    adder.y2 <== y; // c=4

    adder.xout ==> xout; // c=5
    adder.yout ==> yout; // c=6
}
```
4 signals and 6 constraints. The dof of [[BabyAdd]] is 4.

`dof = 2`