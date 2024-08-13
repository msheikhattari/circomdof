```
template SMTProcessor(nLevels) {
    signal input oldRoot; \\s=1
    signal output newRoot; \\s=2
    signal input siblings[nLevels]; \\s=2+n
    signal input oldKey; \\s=3+n
    signal input oldValue; \\s=4+n
    signal input isOld0; \\s=5+n
    signal input newKey; \\s=6+n
    signal input newValue; \\s=7+n
    signal input fnc[2]; \\s=9+n

    signal enabled; \\s=10+n

    var i;

    enabled <== fnc[0] + fnc[1] - fnc[0]*fnc[1]; \\c=1

    component hash1Old = SMTHash1(); \\component
    hash1Old.key <== oldKey; \\c=2
    hash1Old.value <== oldValue; \\c=3

    component hash1New = SMTHash1(); \\component
    hash1New.key <== newKey; \\c=4
    hash1New.value <== newValue; \\c=5

    component n2bOld = Num2Bits_strict(); \\component
    component n2bNew = Num2Bits_strict(); \\component

    n2bOld.in <== oldKey; \\c=6
    n2bNew.in <== newKey; \\c=7

    component smtLevIns = SMTLevIns(nLevels); \\component
    for (i=0; i<nLevels; i++) smtLevIns.siblings[i] <== siblings[i]; \\c=7+n
    smtLevIns.enabled <== enabled;\\c=8+n

    component xors[nLevels]; \\component
    for (i=0; i<nLevels; i++) {
        xors[i] = XOR(); \\component
        xors[i].a <== n2bOld.out[i];
        xors[i].b <== n2bNew.out[i];
    }
    \\c=10+3n

    component sm[nLevels]; \\component
    for (i=0; i<nLevels; i++) {
        sm[i] = SMTProcessorSM(); \\component
        if (i==0) {
            sm[i].prev_top <== enabled;
            sm[i].prev_old0 <== 0;
            sm[i].prev_bot <== 0;
            sm[i].prev_new1 <== 0;
            sm[i].prev_na <== 1-enabled;
            sm[i].prev_upd <== 0;
        } else {
            sm[i].prev_top <== sm[i-1].st_top;
            sm[i].prev_old0 <== sm[i-1].st_old0;
            sm[i].prev_bot <== sm[i-1].st_bot;
            sm[i].prev_new1 <== sm[i-1].st_new1;
            sm[i].prev_na <== sm[i-1].st_na;
            sm[i].prev_upd <== sm[i-1].st_upd;
        }
        \\c=10+9n
        sm[i].is0 <== isOld0;
        sm[i].xor <== xors[i].out;
        sm[i].fnc[0] <== fnc[0];
        sm[i].fnc[1] <== fnc[1];
        sm[i].levIns <== smtLevIns.levIns[i];
        \\c=10+15n
    }
    sm[nLevels-1].st_na + sm[nLevels-1].st_new1 + sm[nLevels-1].st_old0 +sm[nLevels-1].st_upd === 1; \\c=11+15n

    component levels[nLevels]; \\component
    for (i=nLevels-1; i != -1; i--) {
        levels[i] = SMTProcessorLevel(); \\component

        levels[i].st_top <== sm[i].st_top;
        levels[i].st_old0 <== sm[i].st_old0;
        levels[i].st_bot <== sm[i].st_bot;
        levels[i].st_new1 <== sm[i].st_new1;
        levels[i].st_na <== sm[i].st_na;
        levels[i].st_upd <== sm[i].st_upd;

        levels[i].sibling <== siblings[i];
        levels[i].old1leaf <== hash1Old.out;
        levels[i].new1leaf <== hash1New.out;

        levels[i].newlrbit <== n2bNew.out[i];
        if (i==nLevels-1) {
            levels[i].oldChild <== 0;
            levels[i].newChild <== 0;
        } else {
            levels[i].oldChild <== levels[i+1].oldRoot;
            levels[i].newChild <== levels[i+1].newRoot;
        }
        \\c=11+26n
    }

    component topSwitcher = Switcher(); \\component

    topSwitcher.sel <== fnc[0]*fnc[1]; \\c=12+26n
    topSwitcher.L <== levels[0].oldRoot; \\c=13+26n
    topSwitcher.R <== levels[0].newRoot; \\c=14+26n

    component checkOldInput = ForceEqualIfEnabled(); \\component
    checkOldInput.enabled <== enabled; \\c=15+26n
    checkOldInput.in[0] <== oldRoot; \\c=16+26n
    checkOldInput.in[1] <== topSwitcher.outL; \\c=17+26n

    newRoot <== enabled * (topSwitcher.outR - oldRoot) + oldRoot; \\c=18+26n

//    topSwitcher.outL === oldRoot*enabled;
//    topSwitcher.outR === newRoot*enabled;

    // Ckeck keys are equal if updating
    component areKeyEquals = IsEqual(); \\component
    areKeyEquals.in[0] <== oldKey; \\c=19+26n
    areKeyEquals.in[1] <== newKey; \\c=20 + 26n

    component keysOk = MultiAND(3); \\component
    keysOk.in[0] <== 1-fnc[0]; \\21+26n
    keysOk.in[1] <== fnc[1]; \\22+26n
    keysOk.in[2] <== 1-areKeyEquals.out; \\c=23+26n

    keysOk.out === 0; \\c=24+26n
}
```
10 + n signals, 24 + 26n constraints. The dof of [[SMTHash1]] is 2 (called 2 times). The dof of [[Num2Bits_strict]] is -2 (called 2 times). The dof of [[SMTLevIns]] is -2n (called 1 time). The dof of [[XOR]] is 2 (called n times). The dof of [[SMTProcessorSM]] is 11 (called n times). The dof of [[SMTProcessorLevel]] is 12 (called n times). The dof of [[Switcher]] is 3 (called 1 time). The dof of [[ForceEqualIfEnabled]] is 2 (called 1 time). The dof of [[IsEqual]] is 2 (called 1 time). The dof of [[MultiAND]] is 1 (called 1 time). In total: 10 + n - 24 -26n -4 -2n +2n +11n+12n+3+2+2+1= -2n - 10

`dof = -2n - 10`