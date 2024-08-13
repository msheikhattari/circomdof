```
template Sign() {
    signal input in[254]; \\s=254
    signal output sign; \\s=255

    component comp = CompConstant(10944121435919637611123202872628637544274182200208017171849102093287904247808); \\component

    var i;

    for (i=0; i<254; i++) {
        comp.in[i] <== in[i];
    }
    \\c = 254

    sign <== comp.out; \\c=255
}
```
255 signals, 255 constraints. The dof of [[CompConstant]] is 253.

`dof = 253`