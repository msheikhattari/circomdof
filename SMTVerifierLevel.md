```
template SMTVerifierLevel() {
    signal input st_top;
    signal input st_i0;
    signal input st_iold;
    signal input st_inew;
    signal input st_na;

    signal output root;
    signal input sibling;
    signal input old1leaf;
    signal input new1leaf;
    signal input lrbit;
    signal input child;

    signal aux[2];
	\\s =13

    component proofHash = SMTHash2(); \\component 
    component switcher = Switcher(); \\component

    switcher.L <== child;
    switcher.R <== sibling;

    switcher.sel <== lrbit;
    proofHash.L <== switcher.outL;
    proofHash.R <== switcher.outR;

    aux[0] <== proofHash.out * st_top;
    aux[1] <== old1leaf*st_iold;

    root <== aux[0] + aux[1] + new1leaf*st_inew;
    \\c=8
}
```
13 signals, 8 constraints. The dof of [[SMTHash2]] is 2 (called 1 time). The dof of [[Switcher]] is 3 (called 1 time).

`dof = 10`