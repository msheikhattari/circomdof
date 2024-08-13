```
template SMTVerifierSM() {
    signal input is0;
    signal input levIns;
    signal input fnc;

    signal input prev_top;
    signal input prev_i0;
    signal input prev_iold;
    signal input prev_inew;
    signal input prev_na;

    signal output st_top;
    signal output st_i0;
    signal output st_iold;
    signal output st_inew;
    signal output st_na;

    signal prev_top_lev_ins;
    signal prev_top_lev_ins_fnc;
    \\s=15

    prev_top_lev_ins <== prev_top * levIns;
    prev_top_lev_ins_fnc <== prev_top_lev_ins*fnc;  // prev_top * levIns * fnc
    st_top <== prev_top - prev_top_lev_ins;
    st_inew <== prev_top_lev_ins - prev_top_lev_ins_fnc;
    st_iold <== prev_top_lev_ins_fnc * (1 - is0);
    st_i0 <== prev_top_lev_ins * is0;
    st_na <== prev_na + prev_inew + prev_iold + prev_i0;
    \\c=7
}
```
15 signals, 7 constraints.

`dof = 8`