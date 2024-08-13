```
template SMTProcessorSM() {
  signal input xor;
  signal input is0;
  signal input levIns;
  signal input fnc[2];
  \\s=5

  signal input prev_top;
  signal input prev_old0;
  signal input prev_bot;
  signal input prev_new1;
  signal input prev_na;
  signal input prev_upd;
  \\s=11

  signal output st_top;
  signal output st_old0;
  signal output st_bot;
  signal output st_new1;
  signal output st_na;
  signal output st_upd;
  \\s=17

  signal aux1;
  signal aux2;
  \\s=19

  aux1 <==                  prev_top * levIns;
  aux2 <== aux1*fnc[0];  // prev_top * levIns * fnc[0]
  st_top <== prev_top - aux1;
  st_old0 <== aux2 * is0;  // prev_top * levIns * is0 * fnc[0]
  st_new1 <== (aux2 - st_old0 + prev_bot)*xor;
  st_bot <== (1-xor) * (aux2 - st_old0 + prev_bot);
  st_upd <== aux1 - aux2;
  st_na <== prev_new1 + prev_old0 + prev_na + prev_upd;
  \\c=8

}
```
19 signals, 8 constraints.

`dof = 11`