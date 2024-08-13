```
template Point2Bits_Strict() {
    signal input in[2]; \\ s = 2
    signal output out[256]; \\ s = 258

    var i;

    component n2bX = Num2Bits(254); \\ component
    n2bX.in <== in[0]; \\ c = 1
    component n2bY = Num2Bits(254); \\ component
    n2bY.in <== in[1]; \\ c = 2

    component aliasCheckX = AliasCheck(); \\ component
    component aliasCheckY = AliasCheck(); \\component
    for (i=0; i<254; i++) { \\ c = 2 + 2*254
        aliasCheckX.in[i] <== n2bX.out[i];
        aliasCheckY.in[i] <== n2bY.out[i];
    }

    component signCalc = CompConstant(10944121435919637611123202872628637544274182200208017171849102093287904247808); \\ component
    for (i=0; i<254; i++) {
        signCalc.in[i] <== n2bX.out[i]; \\ c = 2 + 3*254
    }

    for (i=0; i<254; i++) { \\ c= 2 + 4*254
        out[i] <== n2bY.out[i];
    }
    out[254] <== 0; \\ c = 3 + 4*254
    out[255] <== signCalc.out; \\ c= 4 + 4*254
}
```
258 signals and 1020 constraints. The dof of [[Num2Bits]] is 0 (called 2 times) and the dof of [[AliasCheck]] is 252 (called 2 times). The dof of [[CompConstant]] is 253 (called 1 time). In total: 256 - 1020 + 504 + 253 = -5.

`dof = -5`
☢️