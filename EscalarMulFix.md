```
template EscalarMulFix(n, BASE) {
    signal input e[n];              // Input in binary format // s=n
    signal output out[2];           // Point (Twisted format) // s=n+2

    var nsegments = (n-1)\246 +1;       // 249 probably would work. But I'm not sure and for security I keep 246
    var nlastsegment = n - (nsegments-1)*249;

    component segments[nsegments]; //component

    component m2e[nsegments-1]; //component
    component adders[nsegments-1]; //component

    var s;
    var i;
    var nseg;
    var nWindows;

    for (s=0; s<nsegments; s++) {

        nseg = (s < nsegments-1) ? 249 : nlastsegment;
        nWindows = ((nseg - 1)\3)+1;

        segments[s] = SegmentMulFix(nWindows); //component

        for (i=0; i<nseg; i++) { // this runs n times across all s. c = n*1 = n
            segments[s].e[i] <== e[s*249+i];
        }

        for (i = nseg; i<nWindows*3; i++) { // runs 0 times while s < nsegments - 1 and x = 3*(((n - (((n-1)\246 +1)-1)*249) - 1)\3)+1 - (n - (((n-1)\246 +1)-1)*249) times on the last run
            segments[s].e[i] <== 0; // c = n + x
        }

        if (s==0) { // runs once
            segments[s].base[0] <== BASE[0]; // c = n+x+1 
            segments[s].base[1] <== BASE[1]; // c = n+x+2
        } else { // runs y = (n-1)\246 times
            m2e[s-1] = Montgomery2Edwards(); // component
            adders[s-1] = BabyAdd(); //component

            segments[s-1].dbl[0] ==> m2e[s-1].in[0]; // 1*y
            segments[s-1].dbl[1] ==> m2e[s-1].in[1]; // 2*y

            m2e[s-1].out[0] ==> segments[s].base[0]; // 3*y
            m2e[s-1].out[1] ==> segments[s].base[1]; // 4*y

            if (s==1) { //runs once
                segments[s-1].out[0] ==> adders[s-1].x1; // 1
                segments[s-1].out[1] ==> adders[s-1].y1; // 2
            } else { // runs y = (n-1)\246 - 1 times
                adders[s-2].xout ==> adders[s-1].x1; // y-1
                adders[s-2].yout ==> adders[s-1].y1; // 2(y-1)
            }
            segments[s].out[0] ==> adders[s-1].x2; // 5*y
            segments[s].out[1] ==> adders[s-1].y2; // 6*y
        }
    }

	// c = n+x+2 + (6y + 2 + 2(y-1)) = n + x + 8y + 2

    if (nsegments == 1) {
        segments[0].out[0] ==> out[0]; 
        segments[0].out[1] ==> out[1];
    } else {
        adders[nsegments-2].xout ==> out[0];
        adders[nsegments-2].yout ==> out[1];
    }
    // above always runs twice, so c = n + x + 8y + 4
}
```
Define $x \equiv$ `3*((((n - ((n-1)\246)*249) - 1)\3) +1) - (n - ((n-1)\246)*249)`
Define $y \equiv$ `(n-1)\246`

There are 2 + n signals. The number of constraints is n + x + 8y + 4. The dof of [[SegmentMulFix]] is 5n - 1 (called over all segments, this is 5n - (y+1)) . The dof of [[Montgomery2Edwards]] is 2 (called y times). The dof is [[BabyAdd]] is 4 (called y times). In total: 2 + n  - (n + x + 8y + 4) + 5n - (y+1) + 2y + 4y = 5n - 3y - x - 3

`dof = 5n - 3y - x - 3`
