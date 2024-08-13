```
template MiMCSponge(nInputs, nRounds, nOutputs) {
  signal input ins[nInputs]; \\ s= nInputs
  signal input k; \\ s= nInputs + 1
  signal output outs[nOutputs]; \\ s= nInputs + 1 + nOutputs

  var i;

  // S = R||C
  component S[nInputs + nOutputs - 1]; \\component

  for (i = 0; i < nInputs; i++) {
    S[i] = MiMCFeistel(nRounds); \\component
    S[i].k <== k; \\ c= nInputs
    if (i == 0) {
      S[i].xL_in <== ins[0];
      S[i].xR_in <== 0;
    } else {
      S[i].xL_in <== S[i-1].xL_out + ins[i];
      S[i].xR_in <== S[i-1].xR_out;
    }
    \\c = 3*nInputs
  }

  outs[0] <== S[nInputs - 1].xL_out; \\c=3*nInputs + 1

  for (i = 0; i < nOutputs - 1; i++) {
    S[nInputs + i] = MiMCFeistel(nRounds); \\component
    S[nInputs + i].k <== k; \\c=3*nInputs + 1 + nOutputs
    S[nInputs + i].xL_in <== S[nInputs + i - 1].xL_out; \\c=3*nInputs + 1 + 2*nOutputs
    S[nInputs + i].xR_in <== S[nInputs + i - 1].xR_out; \\c=3*nInputs + 1 + 3*nOutputs
    outs[i + 1] <== S[nInputs + i].xL_out; \\c=3*nInputs + 1 + 4*nOutputs
  }
}
```
nInputs + 1 + nOutputs signals, `3*nInputs + 1 + 4*nOutputs` constraints. The dof of [[MiMCFeistel]] is 3 (called nInputs + nOutputs - 1 times).

`dof = nInputs - 3`