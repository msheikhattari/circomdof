```
template EscalarMulAny(n) {
    signal input e[n];              // Input in binary format \\ s = n
    signal input p[2];              // Point (Twisted format) \\ s = n + 2
    signal output out[2];           // Point (Twisted format) \\ s = n + 4

    var nsegments = (n-1)\148 +1; \\ let x = nsegments
    var nlastsegment = n - (nsegments-1)*148;

    component segments[nsegments]; \\component
    component doublers[nsegments-1]; \\component
    component m2e[nsegments-1]; \\component
    component adders[nsegments-1]; \\component
    component zeropoint = IsZero(); \\component
    zeropoint.in <== p[0]; \\ c = 1

    var s;
    var i;
    var nseg;

    for (s=0; s<nsegments; s++) {

        nseg = (s < nsegments-1) ? 148 : nlastsegment;

        segments[s] = SegmentMulAny(nseg); \\component

        for (i=0; i<nseg; i++) {
            e[s*148+i] ==> segments[s].e[i]; \\ c = n + 1, runs for all n elements
        }

        if (s==0) {
            // force G8 point if input point is zero
            segments[s].p[0] <== p[0] + (5299619240641551281634865583518297030282874472190772894086521144482721001553 - p[0])*zeropoint.out; \\ c = n + 2
            segments[s].p[1] <== p[1] + (16950150798460657717958625567821834550301663161624707787222815936182638968203 - p[1])*zeropoint.out; \\ c = n + 3
        } else {
            doublers[s-1] = MontgomeryDouble(); \\ component
            m2e[s-1] = Montgomery2Edwards(); \\ component
            adders[s-1] = BabyAdd(); \\ component

            segments[s-1].dbl[0] ==> doublers[s-1].in[0]; \\ c = n + 3 + (x-1)
            segments[s-1].dbl[1] ==> doublers[s-1].in[1]; \\ c = n + 3 + 2(x-1)

            doublers[s-1].out[0] ==> m2e[s-1].in[0]; \\ c = n + 3 + 3(x-1)
            doublers[s-1].out[1] ==> m2e[s-1].in[1]; \\ c = n + 3 + 4(x-1)

            m2e[s-1].out[0] ==> segments[s].p[0]; \\ c = n + 3 + 5(x-1)
            m2e[s-1].out[1] ==> segments[s].p[1]; \\ c = n + 3 + 6(x-1)

            if (s==1) {
                segments[s-1].out[0] ==> adders[s-1].x1;
                segments[s-1].out[1] ==> adders[s-1].y1;
            } else {
                adders[s-2].xout ==> adders[s-1].x1;
                adders[s-2].yout ==> adders[s-1].y1;
            }
            \\ c = n + 3 + 6(x-1) + 2x
            segments[s].out[0] ==> adders[s-1].x2; \\ c = n + 3 + 6(x-1) + 3x
            segments[s].out[1] ==> adders[s-1].y2; \\ c = n + 3 + 6(x-1) + 4x
        }
    }

    if (nsegments == 1) {
        segments[0].out[0]*(1-zeropoint.out) ==> out[0];
        segments[0].out[1]+(1-segments[0].out[1])*zeropoint.out ==> out[1];
    } else {
        adders[nsegments-2].xout*(1-zeropoint.out) ==> out[0];
        adders[nsegments-2].yout+(1-adders[nsegments-2].yout)*zeropoint.out ==> out[1];
    }
    c = n + 5 + 6(x-1) + 3x
}
```
Define $x \equiv$ `(n-1)\148 +1`
n + 4 signals and n + 9x - 1 constraints. The dof of [[IsZero]] is 1 (called 1 time). The dof of [[SegmentMulAny]] is n + 3x. The dof of [[MontgomeryDouble]] is 2 (called x - 1 times). The dof of [[Montgomery2Edwards]] is 2 (called x - 1 times). The dof of [[BabyAdd]] is 4 (called x - 1 times). In total: n + 4 - n - 9x + 1 + 1 + n + 3x + 2(x-1) + 2(x-1) + 4(x-1) = n + 2x - 2.

`dof = n + 2x - 2`
