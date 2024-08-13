```
template MultiAND(n) {
    signal input in[n]; \\s=n
    signal output out; \\s=n+1
    component and1; \\component
    component and2; \\component
    component ands[2]; \\component
    if (n==1) {
        out <== in[0]; \\c=1
    } else if (n==2) {
        and1 = AND(); \\component
        and1.a <== in[0]; \\c=1
        and1.b <== in[1]; \\c=2
        out <== and1.out; \\c=3
    } else {
        and2 = AND(); \\component
        var n1 = n\2; 
        var n2 = n-n\2;
        ands[0] = MultiAND(n1); \\component
        ands[1] = MultiAND(n2); \\component
        var i;
        for (i=0; i<n1; i++) ands[0].in[i] <== in[i]; \\c=n1
        for (i=0; i<n2; i++) ands[1].in[i] <== in[n1+i]; \\c=n1 + n2 = n
        and2.a <== ands[0].out; \\c=n + 1
        and2.b <== ands[1].out; \\c=n + 2
        out <== and2.out; \\c = n + 3
    }
}
```
### n = 1
2 signals, 1 constraint. 

`dof = 1`

### n = 2
3 signals, 3 contraints. The dof of [[AND]] is 2. 

`dof = 2`

### n > 2
n + 1 signals, n + 3 constraints. The dof of [[MultiAND]] is defined recursively with the above base cases, called for both n/2 and n - n/2. Let's check a few cases:

#### n = 3
MultiAND called for 1 and 2, with dof of 1 and 2 respectively. 

`dof = 1`

#### n = 4
MultiAND called for 2 and 2 with dof of 2 and 2 respectively.

`dof = 2`

#### n = 5
MultiAND called for 3 and 2, with dof of 1 and 2 respectively.

`dof = 1`

#### n = 6
MultiAND called for 3 and 3, with dof of 1 and 1 respectively.

`dof = 0`

#### n = 7
MultiAND called for 3 and 4, with dof of 1 and 2 respectively.

`dof = 1`

#### n = 8
MultiAND called for 4 and 4, with dof of 2 and 2 respectively.

`dof = 2`

#### n = 9
MultiAND called for 4 and 5, with dof of 2 and 1 respectively.

`dof = 1`

#### n = 10
MultiAND called for 5 and 5, with dof of 1 and 1 respectively.

`dof = 0`

#### n = 11
MultiAND called for 5 and 6, with dof of 1 and 0 respectively.

`dof = -1`☢️

As n grows larger, dof will exponentially tend towards negative infinity for some numbers. As an illustration, see powers of 2 multiplied by 11:

#### n = 22
MultiAND called for 11 and 11, with dof of -1 and -1 respectively.

`dof = -4`☢️

#### n = 44
MultiAND called for 22 and 22, with dof of -4 and -4 respectively.

`dof = -10`☢️