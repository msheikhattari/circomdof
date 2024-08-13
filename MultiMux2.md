```
template MultiMux2(n) {
    signal input c[n][4];  // Constants \\s=4n
    signal input s[2];   // Selector \\s=4n+2
    signal output out[n]; \\s=5n+2

    signal a10[n]; 
    signal a1[n];
    signal a0[n];
    signal a[n];
    \\s=9n + 2

    signal  s10; \\s=9n+3
    s10 <== s[1] * s[0]; \\c=1

    for (var i=0; i<n; i++) {

          a10[i] <==  ( c[i][ 3]-c[i][ 2]-c[i][ 1]+c[i][ 0] ) * s10;
           a1[i] <==  ( c[i][ 2]-c[i][ 0] ) * s[1];
           a0[i] <==  ( c[i][ 1]-c[i][ 0] ) * s[0];
            a[i] <==  ( c[i][ 0] );

          out[i] <==  (  a10[i] +  a1[i] +  a0[i] +  a[i] );

    }
    \\c = 1 + 5n
}

```
9n + 3 signals, 5n + 1 constraints.

`dof = 4n + 2`