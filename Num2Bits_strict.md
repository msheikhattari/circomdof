```
template Num2Bits_strict() {
    signal input in; \\ s = 1
    signal output out[254]; \\ s = 255

    component aliasCheck = AliasCheck(); \\ component
    component n2b = Num2Bits(254); \\ component
    in ==> n2b.in; \\ c = 1

    for (var i=0; i<254; i++) {
        n2b.out[i] ==> out[i]; \\ c = 255
        n2b.out[i] ==> aliasCheck.in[i]; \\ c = 509
    }
}
```
There are 255 signals and 509 constraints. The dof of [[AliasCheck]] is 252 and the dof of [[Num2Bits]] is 0. In total: 255 - 509 + 252 = -2

`dof = -2`
☢️