```
template LessThan(n) {
    assert(n <= 252); \\ c = 1
    signal input in[2]; \\ s = 3
    signal output out; \\ s = 3

    component n2b = Num2Bits(n+1); \\component

    n2b.in <== in[0]+ (1<<n) - in[1]; \\ c = 2

    out <== 1-n2b.out[n]; \\ c=3
}
```
3 signals and 2 constraints. The dof of [[Num2Bits]] is 0.

`dof = 1`