```
template Multiplexer(wIn, nIn) {
    signal input inp[nIn][wIn]; \\s=nIn*wIn
    signal input sel; \\s=nIn*wIn + 1
    signal output out[wIn];  \\s=nIn*wIn + 1 + wIn
    component dec = Decoder(nIn); \\component
    component ep[wIn]; \\component

    for (var k=0; k<wIn; k++) {
        ep[k] = EscalarProduct(nIn); \\component
    }

    sel ==> dec.inp; \\c=1
    for (var j=0; j<wIn; j++) {
        for (var k=0; k<nIn; k++) {
            inp[k][j] ==> ep[j].in1[k];
            dec.out[k] ==> ep[j].in2[k];
        }
        ep[j].out ==> out[j];
    }
    \\ c = (2*nIn + 1)wIn + 1
    dec.success === 1; \\ c = (2*nIn + 1)wIn + 2
}
```
`nIn*wIn + 1 + wIn` signals and `(2*nIn + 1)wIn + 2` constraints. The dof of [[Decoder]] is 0 (called 1 time). The dof of [[EscalarProduct]] is `2*nIn` (called wIn times).  

`dof = nIn*wIn - 1`