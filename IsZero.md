```
template IsZero() {
    signal input in; \\ s=1
    signal output out; \\ s=2

    signal inv; \\s=3

    inv <-- in!=0 ? 1/in : 0;

    out <== -in*inv +1; \\c=1
    in*out === 0; \\c=2
}
```
There are 3 signals and 2 constraints.

`dof = 1`