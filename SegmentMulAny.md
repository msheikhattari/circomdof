```
template SegmentMulAny(n) {
    signal input e[n]; \\s = n
    signal input p[2]; \\s = n + 2
    signal output out[2]; \\s = n+4
    signal output dbl[2]; \\s = n+6

    component bits[n-1]; \\component

    component e2m = Edwards2Montgomery(); \\component

    p[0] ==> e2m.in[0]; \\ c=1
    p[1] ==> e2m.in[1]; \\ c=2

    var i;

    bits[0] = BitElementMulAny(); \\component
    e2m.out[0] ==> bits[0].dblIn[0]; \\c=3
    e2m.out[1] ==> bits[0].dblIn[1]; \\c=4
    e2m.out[0] ==> bits[0].addIn[0]; \\c=5
    e2m.out[1] ==> bits[0].addIn[1]; \\c=6
    e[1] ==> bits[0].sel; \\c=7

    for (i=1; i<n-1; i++) {
        bits[i] = BitElementMulAny(); \\component

        bits[i-1].dblOut[0] ==> bits[i].dblIn[0]; \\c=6+(n-2)
        bits[i-1].dblOut[1] ==> bits[i].dblIn[1]; \\c=6+2(n-2)
        bits[i-1].addOut[0] ==> bits[i].addIn[0]; \\c=6+3(n-2)
        bits[i-1].addOut[1] ==> bits[i].addIn[1]; \\c=6+4(n-2)
        e[i+1] ==> bits[i].sel; \\c=6+5(n-2)
    }

    bits[n-2].dblOut[0] ==> dbl[0]; \\c=7+5(n-2)
    bits[n-2].dblOut[1] ==> dbl[1]; \\c=8+5(n-2)

    component m2e = Montgomery2Edwards(); \\component

    bits[n-2].addOut[0] ==> m2e.in[0]; \\c=9+5(n-2)
    bits[n-2].addOut[1] ==> m2e.in[1]; \\c=10+5(n-2)

    component eadder = BabyAdd(); \\component

    m2e.out[0] ==> eadder.x1; \\c=11+5(n-2)
    m2e.out[1] ==> eadder.y1; \\c=12+5(n-2)
    -p[0] ==> eadder.x2; \\c=13+5(n-2)
    p[1] ==> eadder.y2; \\c=14+5(n-2)

    component lastSel = Multiplexor2(); \\component

    e[0] ==> lastSel.sel; \\c=15+5(n-2)
    eadder.xout ==> lastSel.in[0][0]; \\c=16+5(n-2)
    eadder.yout ==> lastSel.in[0][1]; \\c=17+5(n-2)
    m2e.out[0] ==> lastSel.in[1][0]; \\c=18+5(n-2)
    m2e.out[1] ==> lastSel.in[1][1]; \\c=19+5(n-2)

    lastSel.out[0] ==> out[0]; \\c=20+5(n-2)
    lastSel.out[1] ==> out[1]; \\c=21+5(n-2)
}
```
n + 6 signals and 5n + 11 constraints. The dof of [[Edwards2Montgomery]] is 2 (called 1 time). The dof of [[BitElementMulAny]] is 5 (called n-1 times). The dof of [[Montgomery2Edwards]] is 2 (called 1 time). The dof of [[BabyAdd]] is 4 (called 1 time). The dof of [[Multiplexor2]] is 5 (called 1 time). In total: n + 6 - 5n - 11 + 2 + 5(n-1) + 2 + 4 + 5 = n + 3.

`dof = n+3`