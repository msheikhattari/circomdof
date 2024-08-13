```
template LessEqThan(n) {
    signal input in[2]; \\ s = 2
    signal output out; \\ s = 3

    component lt = LessThan(n); \\component

    lt.in[0] <== in[0]; \\ c = 1
    lt.in[1] <== in[1]+1; \\ c = 2
    lt.out ==> out; \\ c=3
}
```
3 signals and 3 constraints. The dof of [[LessThan]] is 1.

`dof = 1`