```
template Bits2Num_strict() {
    signal input in[254]; \\ s=254
    signal output out; \\ s = 255

    component aliasCheck = AliasCheck(); \\ component
    component b2n = Bits2Num(254); \\ component

    for (var i=0; i<254; i++) {
        in[i] ==> b2n.in[i]; \\ c=254
        in[i] ==> aliasCheck.in[i]; \\c=508
    }

    b2n.out ==> out; \\c=509
}
```
255 signals and 509 constraints. The dof of [[AliasCheck]] is 252 and the dof of [[Bits2Num]] is 254. In total: 255 - 509 + 252 + 254 = 252

`dof = 252`