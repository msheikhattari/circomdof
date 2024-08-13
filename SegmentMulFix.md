```
template SegmentMulFix(nWindows) {
    signal input e[nWindows*3]; // s = 3n
    signal input base[2]; // s = 3n + 2
    signal output out[2]; // s = 3n + 4
    signal output dbl[2]; // s = 3n + 6

    var i;
    var j;

    // Convert the base to montgomery

    component e2m = Edwards2Montgomery(); // component
    e2m.in[0] <== base[0]; // c = 1
    e2m.in[1] <== base[1]; // c = 2

    component windows[nWindows]; // component 
    component adders[nWindows]; // component
    component cadders[nWindows]; // component

    // In the last step we add an extra doubler so that numbers do not match.
    component dblLast = MontgomeryDouble();

    for (i=0; i<nWindows; i++) { // runs n times
        windows[i] = WindowMulFix(); //component
        cadders[i] = MontgomeryAdd(); //component
        if (i==0) { 
            windows[i].base[0] <== e2m.out[0]; 
            windows[i].base[1] <== e2m.out[1];
            cadders[i].in1[0] <== e2m.out[0];
            cadders[i].in1[1] <== e2m.out[1];
        } else {
            windows[i].base[0] <== windows[i-1].out8[0];
            windows[i].base[1] <== windows[i-1].out8[1];
            cadders[i].in1[0] <== cadders[i-1].out[0];
            cadders[i].in1[1] <== cadders[i-1].out[1];
        } // c = 2 + 4n
        for (j=0; j<3; j++) {
            windows[i].in[j] <== e[3*i+j];
        } // c = 5 + 4n
        if (i<nWindows-1) {
            cadders[i].in2[0] <== windows[i].out8[0];
            cadders[i].in2[1] <== windows[i].out8[1];
            // c = 5 + 4n + 2(n-1)
        } else {
            dblLast.in[0] <== windows[i].out8[0];
            dblLast.in[1] <== windows[i].out8[1];
            cadders[i].in2[0] <== dblLast.out[0];
            cadders[i].in2[1] <== dblLast.out[1];
        }
	        // c = 5 + 4n + 2(n-1) + 4 = 6n + 7
    }

    for (i=0; i<nWindows; i++) { // runs n times
        adders[i] = MontgomeryAdd(); //component
        if (i==0) {
            adders[i].in1[0] <== dblLast.out[0];
            adders[i].in1[1] <== dblLast.out[1];
        } else {
            adders[i].in1[0] <== adders[i-1].out[0];
            adders[i].in1[1] <== adders[i-1].out[1];
        }
        // c = 8n + 7
        adders[i].in2[0] <== windows[i].out[0]; // c = 9n + 7
        adders[i].in2[1] <== windows[i].out[1]; // c = 10n + 7
    }

    component m2e = Montgomery2Edwards(); //component
    component cm2e = Montgomery2Edwards(); //component

    m2e.in[0] <== adders[nWindows-1].out[0]; // c = 10n + 8
    m2e.in[1] <== adders[nWindows-1].out[1]; // c = 10n + 9
    cm2e.in[0] <== cadders[nWindows-1].out[0]; // c = 10n + 10
    cm2e.in[1] <== cadders[nWindows-1].out[1]; // c = 10n + 11

    component cAdd = BabyAdd(); // component
    cAdd.x1 <== m2e.out[0]; // c = 10n + 12
    cAdd.y1 <== m2e.out[1]; // c = 10n + 13
    cAdd.x2 <== -cm2e.out[0]; // c = 10n + 14
    cAdd.y2 <== cm2e.out[1]; // c = 10n + 15

    cAdd.xout ==> out[0]; // c = 10n + 16
    cAdd.yout ==> out[1]; // c = 10n + 17

    windows[nWindows-1].out8[0] ==> dbl[0]; // c = 10n + 18
    windows[nWindows-1].out8[1] ==> dbl[1]; // c = 10n + 19
}
```
There are 3n + 6 signals and 10n + 19 constraints. The dof of [[Edwards2Montgomery]] is 2 (called 1 time). The dof of [[MontgomeryDouble]] is 2 (called 1 time). The dof of [[WindowMulFix]] is 4 (called n times). The dof of [[MontgomeryAdd]] is 4 (called 2n times). The dof of [[Montgomery2Edwards]] is 2 (called 2 times). The dof of [[BabyAdd]] is 4 (called 1 time). In total: 3n + 6 - (10n + 19) + 2 + 2 + 4n + 8n + 4 + 4 = 5n - 1.

`dof = 5n-1`