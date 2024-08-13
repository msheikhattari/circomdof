```
template SMTLevIns(nLevels) {
    signal input enabled; \\s=1
    signal input siblings[nLevels]; \\s=n+1
    signal output levIns[nLevels]; \\s=2n+1
    signal done[nLevels-1];        // Indicates if the insLevel has aready been detected. \\s=3n

    var i;

    component isZero[nLevels]; \\component

    for (i=0; i<nLevels; i++) {
        isZero[i] = IsZero(); \\component
        isZero[i].in <== siblings[i]; 
    }
    \\c=4n

    // The last level must always have a sibling of 0. If not, then it cannot be inserted.
    (isZero[nLevels-1].out - 1) * enabled === 0; \\c=4n+1

    levIns[nLevels-1] <== (1-isZero[nLevels-2].out); \\c=4n+2
    done[nLevels-2] <== levIns[nLevels-1]; \\c=4n+3
    for (i=nLevels-2; i>0; i--) {
        levIns[i] <== (1-done[i])*(1-isZero[i-1].out);
        done[i-1] <== levIns[i] + done[i];
    }
    \\ c=2(n-2) + 4n+3 = 6n-1

    levIns[0] <== (1-done[0]); \\c = 6n
}
```
3n signals, 6n constraints. The dof of [[IsZero]] is 1 (called n times). 

`dof = -2n`
☢️