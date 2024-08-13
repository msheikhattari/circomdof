```
template AliasCheck() {
    signal input in[254]; // s=254

    component  compConstant = CompConstant(-1); //component

    for (var i=0; i<254; i++) in[i] ==> compConstant.in[i]; // c=254
    
    compConstant.out === 0; // c = 255
}
```
254 signals and 255 constraints. The dof of [[CompConstant]] is 253.

`dof = 252`.