```
template MultiMiMC7(nInputs, nRounds) {
    signal input in[nInputs]; \\s = nInputs
    signal input k; \\s = nInputs + 1
    signal output out; \\s = nInputs + 2
    signal r[nInputs +1]; \\s = 2*nInputs + 3

    component mims[nInputs]; \\component

    r[0] <== k; \\c=1
    for (var i=0; i<nInputs; i++) {
        mims[i] = MiMC7(nRounds); \\component
        mims[i].x_in <== in[i]; \\ c = 1+nInputs
        mims[i].k <== r[i]; \\ c = 1+2*nInputs
        r[i+1] <== r[i] + in[i] + mims[i].out; \\ c = 1+3*nInputs
    }

    out <== r[nInputs]; \\ c = 2+3*nInputs
}
```
`2*nInputs + 3` signals and `3*nInputs + 2` constraints. The dof of [[MiMC7]] is 2 (called nInputs times). 

`dof = nInputs + 1`