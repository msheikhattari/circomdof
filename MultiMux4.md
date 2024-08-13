```
template MultiMux4(n) {
    signal input c[n][16];  // Constants \\s=16n
    signal input s[4];   // Selector \\s = 16n + 4
    signal output out[n]; \\s=17n + 4

    signal a3210[n]; \\s=18n+4
    signal a321[n]; \\s=19n+4
    signal a320[n]; \\s=20n+4
    signal a310[n]; \\s=21n+4
    signal a32[n]; \\s=22n+4
    signal a31[n]; \\s=23n+4
    signal a30[n]; \\s=24n+4
    signal a3[n]; \\s=25n+4

    signal a210[n]; 
    signal a21[n];
    signal a20[n];
    signal a10[n];
    signal a2[n];
    signal a1[n];
    signal a0[n];
    signal a[n];
    \\s=33n+4

    // 4 constrains for the intermediary variables
    signal  s10; \\s=33n+5
    s10 <== s[1] * s[0]; \\c=1
    signal  s20; \\s=33n+6
    s20 <== s[2] * s[0]; \\c=2
    signal  s21; \\s=33n+7
    s21 <== s[2] * s[1]; \\c=3
    signal s210; \\s=33n+8
    s210 <==  s21 * s[0]; \\c=4


    for (var i=0; i<n; i++) {

        a3210[i] <==  ( c[i][15]-c[i][14]-c[i][13]+c[i][12] - c[i][11]+c[i][10]+c[i][ 9]-c[i][ 8]
                       -c[i][ 7]+c[i][ 6]+c[i][ 5]-c[i][ 4] + c[i][ 3]-c[i][ 2]-c[i][ 1]+c[i][ 0] ) * s210;
         a321[i] <==  ( c[i][14]-c[i][12]-c[i][10]+c[i][ 8] - c[i][ 6]+c[i][ 4]+c[i][ 2]-c[i][ 0] ) * s21;
         a320[i] <==  ( c[i][13]-c[i][12]-c[i][ 9]+c[i][ 8] - c[i][ 5]+c[i][ 4]+c[i][ 1]-c[i][ 0] ) * s20;
         a310[i] <==  ( c[i][11]-c[i][10]-c[i][ 9]+c[i][ 8] - c[i][ 3]+c[i][ 2]+c[i][ 1]-c[i][ 0] ) * s10;
          a32[i] <==  ( c[i][12]-c[i][ 8]-c[i][ 4]+c[i][ 0] ) * s[2];
          a31[i] <==  ( c[i][10]-c[i][ 8]-c[i][ 2]+c[i][ 0] ) * s[1];
          a30[i] <==  ( c[i][ 9]-c[i][ 8]-c[i][ 1]+c[i][ 0] ) * s[0];
           a3[i] <==  ( c[i][ 8]-c[i][ 0] );

         a210[i] <==  ( c[i][ 7]-c[i][ 6]-c[i][ 5]+c[i][ 4] - c[i][ 3]+c[i][ 2]+c[i][ 1]-c[i][ 0] ) * s210;
          a21[i] <==  ( c[i][ 6]-c[i][ 4]-c[i][ 2]+c[i][ 0] ) * s21;
          a20[i] <==  ( c[i][ 5]-c[i][ 4]-c[i][ 1]+c[i][ 0] ) * s20;
          a10[i] <==  ( c[i][ 3]-c[i][ 2]-c[i][ 1]+c[i][ 0] ) * s10;
           a2[i] <==  ( c[i][ 4]-c[i][ 0] ) * s[2];
           a1[i] <==  ( c[i][ 2]-c[i][ 0] ) * s[1];
           a0[i] <==  ( c[i][ 1]-c[i][ 0] ) * s[0];
            a[i] <==  ( c[i][ 0] );

          out[i] <== ( a3210[i] + a321[i] + a320[i] + a310[i] + a32[i] + a31[i] + a30[i] + a3[i] ) * s[3] +
                     (  a210[i] +  a21[i] +  a20[i] +  a10[i] +  a2[i] +  a1[i] +  a0[i] +  a[i] );
    }
    \\c = 17*n + 4
}
```
33n + 8 signals, 17n + 4 constraints.

`dof = 16n + 4`