```
template EdDSAMiMCSpongeVerifier() {
    signal input enabled; \\s=1
    signal input Ax; \\s=2
    signal input Ay; \\s=3

    signal input S; \\s=4
    signal input R8x; \\s=5
    signal input R8y; \\s=6

    signal input M; \\s=7

    var i;

// Ensure S<Subgroup Order

    component snum2bits = Num2Bits(253); \\component
    snum2bits.in <== S; \\c=1

    component  compConstant = CompConstant(2736030358979909402780800718157159386076813972158567259200215660948447373040); \\component

    for (i=0; i<253; i++) {
        snum2bits.out[i] ==> compConstant.in[i]; \\c=254
    }
    compConstant.in[253] <== 0; \\c=255
    compConstant.out === 0; \\c=256

// Calculate the h = H(R,A, msg)

    component hash = MiMCSponge(5, 220, 1); \\component
    hash.ins[0] <== R8x; \\c=257
    hash.ins[1] <== R8y; \\c=258
    hash.ins[2] <== Ax; \\c=259
    hash.ins[3] <== Ay; \\c=260
    hash.ins[4] <== M; \\c=261
    hash.k <== 0; \\c=262

    component h2bits = Num2Bits_strict(); \\component
    h2bits.in <== hash.outs[0]; \\c=263

// Calculate second part of the right side:  right2 = h*8*A

    // Multiply by 8 by adding it 3 times.  This also ensure that the result is in
    // the subgroup.
    component dbl1 = BabyDbl(); \\component
    dbl1.x <== Ax; \\c=264
    dbl1.y <== Ay; \\c=265
    component dbl2 = BabyDbl(); \\component 
    dbl2.x <== dbl1.xout; \\c=266
    dbl2.y <== dbl1.yout; \\c=267
    component dbl3 = BabyDbl(); \\component
    dbl3.x <== dbl2.xout; \\c=268
    dbl3.y <== dbl2.yout; \\c=269

    // We check that A is not zero.
    component isZero = IsZero(); \\component
    isZero.in <== dbl3.x; \\c=270
    isZero.out === 0; \\c=271

    component mulAny = EscalarMulAny(254); \\component
    for (i=0; i<254; i++) { \\c= 525
        mulAny.e[i] <== h2bits.out[i]; 
    }
    mulAny.p[0] <== dbl3.xout; \\c=526
    mulAny.p[1] <== dbl3.yout; \\c=527


// Compute the right side: right =  R8 + right2

    component addRight = BabyAdd(); \\component
    addRight.x1 <== R8x; \\c=528
    addRight.y1 <== R8y; \\c=529
    addRight.x2 <== mulAny.out[0]; \\c=530
    addRight.y2 <== mulAny.out[1]; \\c=531

// Calculate left side of equation left = S*B8

    var BASE8[2] = [
        5299619240641551281634865583518297030282874472190772894086521144482721001553,
        16950150798460657717958625567821834550301663161624707787222815936182638968203
    ];
    component mulFix = EscalarMulFix(253, BASE8); \\component
    for (i=0; i<253; i++) {
        mulFix.e[i] <== snum2bits.out[i]; \\c=784
    }

// Do the comparation left == right if enabled;

    component eqCheckX = ForceEqualIfEnabled(); \\component
    eqCheckX.enabled <== enabled; \\c=785
    eqCheckX.in[0] <== mulFix.out[0]; \\c=786
    eqCheckX.in[1] <== addRight.xout; \\c=787

    component eqCheckY = ForceEqualIfEnabled(); \\component
    eqCheckY.enabled <== enabled; \\c=788
    eqCheckY.in[0] <== mulFix.out[1]; \\c=789
    eqCheckY.in[1] <== addRight.yout; \\c=790
}
```
7 signals and 790 constraints. The dof of [[Num2Bits]] is 0 (called 1 time). The dof of [[CompConstant]] is 253 (called 1 time). The dof of [[MiMCSponge]] is 2 (called 1 time). The dof of [[Num2Bits_strict]] is -2 (called 1 time). The dof of [[BabyDbl]] is 2 (called 3 times). The dof of [[IsZero]] is 1 (called 1 time). The dof of [[EscalarMulAny]] is 256 (called 1 time). The dof of [[BabyAdd]] is 4 (called 1 time). The dof of [[EscalarMulFix]] is 1262 (called 1 time). The dof of [[ForceEqualIfEnabled]] is 2 (called 2 times). In total: 7 - 790 + 253 + 2 -2 + 6 + 1 + 6 + 1 + 256 + 4 + 1262 + 4 = 1010.

`dof = 1010`