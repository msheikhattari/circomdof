```
template BabyPbk() {
    signal input  in; // s=1
    signal output Ax; // s=2
    signal output Ay; // s=3

    var BASE8[2] = [
        5299619240641551281634865583518297030282874472190772894086521144482721001553,
        16950150798460657717958625567821834550301663161624707787222815936182638968203
    ];

    component pvkBits = Num2Bits(253); //component
    pvkBits.in <== in; // c=1

    component mulFix = EscalarMulFix(253, BASE8); //component

    var i;
    for (i=0; i<253; i++) { // c=253*1 + 1 = 254
        mulFix.e[i] <== pvkBits.out[i];
    }
    Ax  <== mulFix.out[0]; // c=255
    Ay  <== mulFix.out[1]; // c=256
}
```
3 signals and 256 constraints. The dof of [[Num2Bits]] is 0. The dof of [[EscalarMulFix]] is 1258 (using n = 253). In total: 3 - 256 + 0 + 1258 = 1005.

`dof = 1005`