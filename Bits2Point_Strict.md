```
template Bits2Point_Strict() {
    signal input in[256]; \\ s = 256
    signal output out[2]; \\ s = 258

    var i;

    // Check aliasing
    component aliasCheckY = AliasCheck(); \\ component
    for (i=0; i<254; i++) {
        aliasCheckY.in[i] <== in[i]; \\ c = 254
    }
    in[254] === 0;

    component b2nY = Bits2Num(254); \\component
    for (i=0; i<254; i++) {
        b2nY.in[i] <== in[i]; \\ c = 508
    }

    out[1] <== b2nY.out; \\ c = 509

    var a = 168700;
    var d = 168696;

    var y2 = out[1] * out[1];

    var x = sqrt(   (1-y2)/(a - d*y2)  );

    if (in[255] == 1) x = -x;

    out[0] <-- x;

    component babyCheck = BabyCheck(); \\component
    babyCheck.x <== out[0]; \\ c = 510
    babyCheck.y <== out[1]; \\ c = 511

    component n2bX = Num2Bits(254); \\ component
    n2bX.in <== out[0]; \\ c = 512
    component aliasCheckX = AliasCheck(); \\component
    for (i=0; i<254; i++) {
        aliasCheckX.in[i] <== n2bX.out[i]; \\ c = 765
    }

    component signCalc = CompConstant(10944121435919637611123202872628637544274182200208017171849102093287904247808); \\ component
    for (i=0; i<254; i++) {
        signCalc.in[i] <== n2bX.out[i]; \\ c = 1019
    }

    signCalc.out === in[255]; \\ c = 1020
}
```
258 signals and 1020 constraints. The dof of [[AliasCheck]] is 252 (called 2 times). The dof of [[Bits2Num]] is 254 (called 1 time). The dof of [[BabyCheck]] is 1 (called 1 time). The dof of [[Num2Bits]] is 0 (called 1 time). The dof of [[CompConstant]] is 253 (called 1 time). In total: 258 - 1020 + (504 + 254 + 1 + 0 + 253) = 250

`dof = 250`