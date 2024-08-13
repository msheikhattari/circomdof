```
template MultiMux3(n) {
    signal input c[n][8];  // Constants // s = 8n
    signal input s[3];   // Selector // s = 8n + 3
    signal output out[n]; // s = 9n+3

    signal a210[n]; // s = 10n+3
    signal a21[n]; // s= 11n + 3
    signal a20[n]; // s = 12n + 3
    signal a2[n]; // s = 13n + 3

    signal a10[n]; // s = 14n+3
    signal a1[n]; // s = 15n+3
    signal a0[n]; // s=16n+3
    signal a[n]; // s=17n+3

    // 4 constrains for the intermediary variables
    signal  s10; // s = 17n+4
    s10 <== s[1] * s[0]; // c = 1

    for (var i=0; i<n; i++) { // c = 1 + n*9

         a210[i] <==  ( c[i][ 7]-c[i][ 6]-c[i][ 5]+c[i][ 4] - c[i][ 3]+c[i][ 2]+c[i][ 1]-c[i][ 0] ) * s10;
          a21[i] <==  ( c[i][ 6]-c[i][ 4]-c[i][ 2]+c[i][ 0] ) * s[1];
          a20[i] <==  ( c[i][ 5]-c[i][ 4]-c[i][ 1]+c[i][ 0] ) * s[0];
           a2[i] <==  ( c[i][ 4]-c[i][ 0] );

          a10[i] <==  ( c[i][ 3]-c[i][ 2]-c[i][ 1]+c[i][ 0] ) * s10;
           a1[i] <==  ( c[i][ 2]-c[i][ 0] ) * s[1];
           a0[i] <==  ( c[i][ 1]-c[i][ 0] ) * s[0];
            a[i] <==  ( c[i][ 0] );

          out[i] <== ( a210[i] + a21[i] + a20[i] + a2[i] ) * s[2] +
                     (  a10[i] +  a1[i] +  a0[i] +  a[i] );

    }
}
```
There are 17n+4 signals and 9n + 1 constraints.

`dof = 8n + 3`