```
template EscalarMul(n, base) {
    signal input in[n]; \\s=n
    signal input inp[2];   // Point input to be added \\s = n+2
    signal output out[2]; \\s= n+4

    var nBlocks = ((n-1)>>2)+1;
    var i;
    var j;

    component windows[nBlocks]; \\component

    // Construct the windows
    for (i=0; i<nBlocks; i++) {
      windows[i] = EscalarMulWindow(base, i);
    }

    // Connect the selectors
    for (i=0; i<nBlocks; i++) {
        for (j=0; j<4; j++) {
            if (i*4+j >= n) {
                windows[i].sel[j] <== 0;
            } else {
                windows[i].sel[j] <== in[i*4+j];
            }
        }
    }
    \\ c = 4((n-1)/4 + 1) = n+3

    // Start with generator
    windows[0].in[0] <== inp[0]; \\ c = n+4
    windows[0].in[1] <== inp[1]; \\ c = n+5

    for(i=0; i<nBlocks-1; i++) {
        windows[i].out[0] ==> windows[i+1].in[0]; 
        windows[i].out[1] ==> windows[i+1].in[1];
    }
    \\ c = n+5 + 2((n-1)/4 - 1)

    windows[nBlocks-1].out[0] ==> out[0]; \\ c = n + 4 + (n-1)/2
    windows[nBlocks-1].out[1] ==> out[1]; \\ c = n + 5 + (n-1)/2
}
```
n + 4 signals, n + 5 + (n-1)/2 constraints. The dof of [[EscalarMulWindow]] is 4 (called (n-1)/4 + 1 times). 

`dof = n + 2 - (n-1)/2`