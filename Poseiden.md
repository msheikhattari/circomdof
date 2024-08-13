```
template Poseidon(nInputs) {
    signal input inputs[nInputs]; \\s=n
    signal output out; \\s=n+1

    component pEx = PoseidonEx(nInputs, 1); \\component
    pEx.initialState <== 0; \\c=1
    for (var i=0; i<nInputs; i++) {
        pEx.inputs[i] <== inputs[i]; \\c=n+1
    }
    out <== pEx.out[0]; \\c=n+2
}
```
n+1 signals, n+2 constraints. The dof of [[PoseidenEx]] is `nInputs(17 + N_ROUNDS_P[nInputs - 1]) + 5 + N_ROUNDS_P[nInputs - 1]` (called 1 time).

`dof = nInputs(17 + N_ROUNDS_P[nInputs - 1]) + 4 + N_ROUNDS_P[nInputs - 1]`