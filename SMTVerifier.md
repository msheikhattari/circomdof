```
template SMTVerifier(nLevels) {
    signal input enabled;
    signal input root;
    signal input siblings[nLevels];
    signal input oldKey;
    signal input oldValue;
    signal input isOld0;
    signal input key;
    signal input value;
    signal input fnc;
    \\ s=n+8

    var i;

    component hash1Old = SMTHash1(); \\component
    hash1Old.key <== oldKey;
    hash1Old.value <== oldValue;
    \\c=2

    component hash1New = SMTHash1(); \\component
    hash1New.key <== key;
    hash1New.value <== value;
    \\c=4

    component n2bOld = Num2Bits_strict(); \\component
    component n2bNew = Num2Bits_strict(); \\component

    n2bOld.in <== oldKey;
    n2bNew.in <== key;
    \\c=6

    component smtLevIns = SMTLevIns(nLevels); \\component
    for (i=0; i<nLevels; i++) smtLevIns.siblings[i] <== siblings[i]; \\c=6+n
    smtLevIns.enabled <== enabled;\\c=7+n

    component sm[nLevels]; \\component
    for (i=0; i<nLevels; i++) {
        sm[i] = SMTVerifierSM(); \\component
        if (i==0) {
            sm[i].prev_top <== enabled;
            sm[i].prev_i0 <== 0;
            sm[i].prev_inew <== 0;
            sm[i].prev_iold <== 0;
            sm[i].prev_na <== 1-enabled;
        } else {
            sm[i].prev_top <== sm[i-1].st_top;
            sm[i].prev_i0 <== sm[i-1].st_i0;
            sm[i].prev_inew <== sm[i-1].st_inew;
            sm[i].prev_iold <== sm[i-1].st_iold;
            sm[i].prev_na <== sm[i-1].st_na;
        }
        sm[i].is0 <== isOld0;
        sm[i].fnc <== fnc;
        sm[i].levIns <== smtLevIns.levIns[i];
    }
    \\c = 9n+7
    sm[nLevels-1].st_na + sm[nLevels-1].st_iold + sm[nLevels-1].st_inew + sm[nLevels-1].st_i0 === 1; \\c=9n+8

    component levels[nLevels]; \\component
    for (i=nLevels-1; i != -1; i--) {
        levels[i] = SMTVerifierLevel(); \\component

        levels[i].st_top <== sm[i].st_top;
        levels[i].st_i0 <== sm[i].st_i0;
        levels[i].st_inew <== sm[i].st_inew;
        levels[i].st_iold <== sm[i].st_iold;
        levels[i].st_na <== sm[i].st_na;

        levels[i].sibling <== siblings[i];
        levels[i].old1leaf <== hash1Old.out;
        levels[i].new1leaf <== hash1New.out;

        levels[i].lrbit <== n2bNew.out[i];
        if (i==nLevels-1) {
            levels[i].child <== 0;
        } else {
            levels[i].child <== levels[i+1].root;
        }
    }
    \\c=19n+7


    // Check that if checking for non inclussuin and isOld0==0 then key!=old
    component areKeyEquals = IsEqual(); \\component
    areKeyEquals.in[0] <== oldKey; \\c=19n+8
    areKeyEquals.in[1] <== key; \\c=19n+9

    component keysOk = MultiAND(4); \\component
    keysOk.in[0] <== fnc;
    keysOk.in[1] <== 1-isOld0;
    keysOk.in[2] <== areKeyEquals.out;
    keysOk.in[3] <== enabled;

    keysOk.out === 0;
     \\c=19n+14

    // Check the root
    component checkRoot = ForceEqualIfEnabled(); \\component
    checkRoot.enabled <== enabled;
    checkRoot.in[0] <== levels[0].root;
    checkRoot.in[1] <== root;
    \\ c=19n+17

    // levels[0].root === root;

}
```
n + 8 signals, 19n + 17 constraints. The dof of [[SMTHash1]] is 2 (called 2 times). The dof of [[Num2Bits_strict]] is -2 (called 2 times). The dof of [[SMTLevIns]] is -2n. The dof of [[SMTVerifierSM]] is 8 (called n times). The dof of [[SMTVerifierLevel]] is 10 (called n times). The dof of [[IsEqual]] is 2 (called 1 time). The dof of [[MultiAND]] is 2 (called 1 time). The dof of [[ForceEqualIfEnabled]] is 2 (called 1 time). In total: n + 8 - 19n - 17 + 4 -4 -2n +8n +10n+2+2+2 = -2n -1

`dof = -2n - 1`
☢️