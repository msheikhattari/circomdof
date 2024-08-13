```
template ForceEqualIfEnabled() {
    signal input enabled; \\ s = 1
    
    signal input in[2]; \\ s=3

    component isz = IsZero(); \\component

    in[1] - in[0] ==> isz.in; \\c=1

    (1 - isz.out)*enabled === 0; \\c=2
}
```
3 signals and 2 constraints. The dof of [[IsZero]] is 1.

`dof = 2`