```
template EdDSAVerifier(n) {
    signal input msg[n]; \\ s = n

    signal input A[256]; \\ s = n + 256
    signal input R8[256]; \\ s = n + 512
    signal input S[256]; \\ s = n + 768

    signal Ax; \\ s = n + 769
    signal Ay; \\ s = n + 770

    signal R8x; \\ s = n + 771
    signal R8y; \\ s = n + 772

    var i;

// Ensure S<Subgroup Order

    component  compConstant = CompConstant(2736030358979909402780800718157159386076813972158567259200215660948447373040); \\ component

    for (i=0; i<254; i++) {
        S[i] ==> compConstant.in[i]; \\ c = 254
    }
    compConstant.out === 0; \\ c = 255
    S[254] === 0; \\ c = 256
    S[255] === 0; \\ c = 257

// Convert A to Field elements (And verify A)

    component bits2pointA = Bits2Point_Strict(); \\component

    for (i=0; i<256; i++) {
        bits2pointA.in[i] <== A[i]; \\ c = 513
    }
    Ax <== bits2pointA.out[0]; \\ c = 514
    Ay <== bits2pointA.out[1]; \\ c = 515

// Convert R8 to Field elements (And verify R8)

    component bits2pointR8 = Bits2Point_Strict(); \\ component

    for (i=0; i<256; i++) {
        bits2pointR8.in[i] <== R8[i]; \\ c = 771
    }
    R8x <== bits2pointR8.out[0]; \\ c = 772
    R8y <== bits2pointR8.out[1]; \\ c = 773

// Calculate the h = H(R,A, msg)

    component hash = Pedersen(512+n); \\ component

    for (i=0; i<256; i++) {
        hash.in[i] <== R8[i]; \\ c = 1285
        hash.in[256+i] <== A[i]; \\ c = 1797
    }
    for (i=0; i<n; i++) {
        hash.in[512+i] <== msg[i]; \\ c = 1797 + n
    }

    component point2bitsH = Point2Bits_Strict(); \\ component
    point2bitsH.in[0] <== hash.out[0]; \\ c = 1798 + n
    point2bitsH.in[1] <== hash.out[1]; \\ c = 1799 + n

// Calculate second part of the right side:  right2 = h*8*A

    // Multiply by 8 by adding it 3 times.  This also ensure that the result is in
    // the subgroup.
    component dbl1 = BabyDbl(); \\ component
    dbl1.x <== Ax; \\ c = 1800 + n
    dbl1.y <== Ay; \\ c = 1801 + n
    component dbl2 = BabyDbl(); \\ component
    dbl2.x <== dbl1.xout; \\ c = 1802 + n
    dbl2.y <== dbl1.yout; \\ c = 1803 + n
    component dbl3 = BabyDbl(); \\ component
    dbl3.x <== dbl2.xout; \\ c = 1804 + n
    dbl3.y <== dbl2.yout; \\ c = 1805 + n

    // We check that A is not zero.
    component isZero = IsZero(); \\ component
    isZero.in <== dbl3.x; \\ c = 1806 + n
    isZero.out === 0; \\ c = 1807 + n

    component mulAny = EscalarMulAny(256); \\ component
    for (i=0; i<256; i++) {
        mulAny.e[i] <== point2bitsH.out[i]; \\ c = 2063 + n
    }
    mulAny.p[0] <== dbl3.xout; \\ c = 2064 + n
    mulAny.p[1] <== dbl3.yout; \\ c = 2065 + n


// Compute the right side: right =  R8 + right2

    component addRight = BabyAdd(); \\ component
    addRight.x1 <== R8x; \\ 2066 + n
    addRight.y1 <== R8y; \\ 2067 + n 
    addRight.x2 <== mulAny.out[0]; \\ 2068 + n
    addRight.y2 <== mulAny.out[1]; \\ 2069 + n

// Calculate left side of equation left = S*B8

    var BASE8[2] = [
        5299619240641551281634865583518297030282874472190772894086521144482721001553,
        16950150798460657717958625567821834550301663161624707787222815936182638968203
    ];
    component mulFix = EscalarMulFix(256, BASE8); \\ component
    for (i=0; i<256; i++) {
        mulFix.e[i] <== S[i]; \\ c = 2325 + n
    }

// Do the comparation left == right

    mulFix.out[0] === addRight.xout; \\ c = 2326 + n
    mulFix.out[1] === addRight.yout; \\ c = 2327 + n
}
```

n + 772 signals and n + 2327 constraints. The dof of [[CompConstant]] is 253 (called 1 time). The dof of [[Bits2Point_Strict]] is 250 (called 2 times). The dof of [[Pedersen]] is 2048 + 4n - 2(((n + 511)/200) + 1) - ((12 + n) % 200) + 2 = 2048 + 4n - 2((n + 511)/200) - ((12 + n) % 200). The dof of [[Point2Bits_Strict]] is -5 (called 1 time). The dof of [[BabyDbl]] is 2 (called 3 times). The dof of [[IsZero]] is 1 (called 1 time). The dof of [[EscalarMulAny]] is 258 (called 1 time). The dof of [[BabyAdd]] is 4 (called 1 time). The dof of [[EscalarMulFix]] is 1273. In total: n + 772 - n - 2327 + 253 + 250 + 2048 + 4n - 2((n + 511)/200) - ((12 + n) % 200) - 5 + 6 + 1 + 258 + 4 + 1273 = 4n - 2((n + 511)/200) - ((12 + n) % 200) + 2533

`dof = 4n - 2((n + 511)/200) - ((12 + n) % 200) + 2533`