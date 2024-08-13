```
template SMTProcessorLevel() {
    signal input st_top;
    signal input st_old0;
    signal input st_bot;
    signal input st_new1;
    signal input st_na;
    signal input st_upd;
    \\s=6

    signal output oldRoot;
    signal output newRoot;
    signal input sibling;
    signal input old1leaf;
    signal input new1leaf;
    signal input newlrbit;
    signal input oldChild;
    signal input newChild;
    \\s=14

    signal aux[4]; \\s=18

    component oldProofHash = SMTHash2(); \\component
    component newProofHash = SMTHash2(); \\component

    component oldSwitcher = Switcher(); \\component
    component newSwitcher = Switcher(); \\component

    // Old side

    oldSwitcher.L <== oldChild; \\c=1
    oldSwitcher.R <== sibling; \\c=2

    oldSwitcher.sel <== newlrbit; \\c=3
    oldProofHash.L <== oldSwitcher.outL; \\c=4
    oldProofHash.R <== oldSwitcher.outR; \\c=5

    aux[0] <== old1leaf * (st_bot + st_new1 + st_upd); \\c=6
    oldRoot <== aux[0] +  oldProofHash.out * st_top; \\c=7

    // New side

    aux[1] <== newChild * ( st_top + st_bot); \\c=8
    newSwitcher.L <== aux[1] + new1leaf*st_new1; \\c=9

    aux[2] <== sibling*st_top; \\c=10
    newSwitcher.R <== aux[2] + old1leaf*st_new1; \\c=11

    newSwitcher.sel <== newlrbit; \\c=12
    newProofHash.L <== newSwitcher.outL; \\c=13
    newProofHash.R <== newSwitcher.outR; \\c=14

    aux[3] <== newProofHash.out * (st_top + st_bot + st_new1); \\c=15
    newRoot <==  aux[3] + new1leaf * (st_old0 + st_upd); \\c=16
}
```
18 signals, 16 constraints. The dof of [[SMTHash2]] is 2 (called 2 times). The dof of [[Switcher]] is 3 (called 2 times).

`dof = 12`