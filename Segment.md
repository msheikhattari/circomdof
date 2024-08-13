```
template Segment(nWindows) {
    signal input in[nWindows*4]; \\ s = 4n
    signal input base[2]; \\ s = 4n + 2
    signal output out[2]; \\ s = 4n + 4

    var i;
    var j;

    // Convert the base to montgomery

    component e2m = Edwards2Montgomery(); \\ component
    e2m.in[0] <== base[0]; \\ c = 1
    e2m.in[1] <== base[1]; \\ c = 2

    component windows[nWindows]; \\ component
    component doublers1[nWindows-1]; \\ component
    component doublers2[nWindows-1]; \\ component
    component adders[nWindows-1]; \\ component
    for (i=0; i<nWindows; i++) {
        windows[i] = Window4(); \\ component
        for (j=0; j<4; j++) {
            windows[i].in[j] <== in[4*i+j]; \\ c = 2 + 4n
        }
        if (i==0) {
            windows[i].base[0] <== e2m.out[0]; \\ c = 3 + 4n
            windows[i].base[1] <== e2m.out[1]; \\ c = 4 + 4n
        } else {
            doublers1[i-1] = MontgomeryDouble(); \\component
            doublers2[i-1] = MontgomeryDouble(); \\component
            doublers1[i-1].in[0] <== windows[i-1].out8[0]; \\c = 4 + 4n + (n-1)
            doublers1[i-1].in[1] <== windows[i-1].out8[1]; \\c = 4 + 4n + 2(n-1)
            doublers2[i-1].in[0] <== doublers1[i-1].out[0]; \\c = 4 + 4n + 3(n-1)
            doublers2[i-1].in[1] <== doublers1[i-1].out[1]; \\c = 4 + 4n + 4(n-1)

            windows[i].base[0] <== doublers2[i-1].out[0]; \\c = 4 + 4n + 5(n-1)
            windows[i].base[1] <== doublers2[i-1].out[1]; \\c = 4 + 4n + 6(n-1)

            adders[i-1] = MontgomeryAdd(); \\component
            if (i==1) {
                adders[i-1].in1[0] <== windows[0].out[0]; 
                adders[i-1].in1[1] <== windows[0].out[1];
            } else {
                adders[i-1].in1[0] <== adders[i-2].out[0];
                adders[i-1].in1[1] <== adders[i-2].out[1];
            }
            \\ c = 4 + 4n + 8(n-1)
            adders[i-1].in2[0] <== windows[i].out[0]; \\ c = 4 + 4n + 9(n-1)
            adders[i-1].in2[1] <== windows[i].out[1]; \\ c = 4 + 4n + 10(n-1)
        }
    }

    component m2e = Montgomery2Edwards(); \\component

    if (nWindows > 1) {
        m2e.in[0] <== adders[nWindows-2].out[0]; 
        m2e.in[1] <== adders[nWindows-2].out[1];
    } else {
        m2e.in[0] <== windows[0].out[0];
        m2e.in[1] <== windows[0].out[1];
    }
    \\ c = 6 + 4n + 10(n-1)

    out[0] <== m2e.out[0]; \\ c = 7 + 4n + 10(n-1)
    out[1] <== m2e.out[1]; \\ c = 8 + 4n + 10(n-1)
}
```
4n + 4 signals and 14n - 2 constraints. The dof of [[Edwards2Montgomery]] is 2 (called 1 time). The dof of [[Window4]] is 6 (called n times). The dof of [[MontgomeryDouble]] is 2 (called 2(n-1) times). The dof of [[MontgomeryAdd]] is 4 (called n-1 times). The dof of [[Montgomery2Edwards]] is 2 (called 1 time). In total: 4n + 4 - (14n - 2) + 2 + 6n + 4(n-1) + 4(n-1) + 2 = 4n + 2.

`dof = 4n + 2`
