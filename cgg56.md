# Key matrix for gcg56
<!---
vim:set tw=79 ts=4 sts=4 sw=4 et nosi filetype=markdown:
-->

## Physical and Electrical Key Positions

The electric wiring column and row positions are described as below in
"Column/Row" order in the physical key positions.

Please note:
* Physical matrix: 4 rows * 14 columns
* Electrical matrix: 4 rows * 14 columns

This allows to scan 54 keys only with 18 pins.

### Top view

This top view can be used for software key address configuration.

```
 Q-side                                                P-side
 ┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
 │0/0│1/0│2/0│3/0│4/0│5/0│6/0│USB│7/0│8/0│9/0│A/0│B/0│C/0│D/0│
 ├───┼───┼───┼───┼───┼───┼───┤   ├───┼───┼───┼───┼───┼───┼───┤
 │0/1│1/1│2/1│3/1│4/1│5/1│6/1│   │7/1│8/1│9/1│A/1│B/1│C/1│D/1│
 ├───┼───┼───┼───┼───┼───┼───┤MCU├───┼───┼───┼───┼───┼───┼───┤
 │0/2│1/2│2/2│3/2│4/2│5/2│6/2│   │7/2│8/2│9/2│A/2│B/2│C/2│D/2│
 ├───┼───┼───┼───┼───┼───┼───┤   ├───┼───┼───┼───┼───┼───┼───┤
 │0/3│1/3│2/3│3/3│4/3│5/3│6/3│   │7/3│8/3│9/3│A/3│B/3│C/3│D/3│
 └───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘
```

This is useful view for writing software for the key input conversion.

### Bottom view

This bottom view can be used for wiring key switches.

```
 P-side                                                Q-side
 ┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
 │D/0│C/0│B/0│A/0│9/0│8/0│7/0│USB│6/0│5/0│4/0│3/0│2/0│1/0│0/0│
 ├───┼───┼───┼───┼───┼───┼───┤   ├───┼───┼───┼───┼───┼───┼───┤
 │D/1│C/1│B/1│A/1│9/1│8/1│7/1│   │6/1│5/1│4/1│3/1│2/1│1/1│0/1│
 ├───┼───┼───┼───┼───┼───┼───┤MCU├───┼───┼───┼───┼───┼───┼───┤
 │D/2│C/2│B/2│A/2│9/2│8/2│7/2│   │6/2│5/2│4/2│3/2│2/2│1/2│0/2│
 ├───┼───┼───┼───┼───┼───┼───┤   ├───┼───┼───┼───┼───┼───┼───┤
 │D/3│C/3│B/3│A/3│9/3│8/3│7/3│   │6/3│5/3│4/3│3/3│2/3│1/3│0/3│
 └───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘
```

This is useful view for wiring and building the hardware.

## Wiring of Switches

This section always uses bottom view unless otherwise noted.

### Global simplified view of wiring

Here is a global simplified view of wiring of switches (bottom view):

```
 P-side                                                Q-side

        CD  CC  CB  CA  C9  C8  C7    C6  C5  C4  C3  C2  C1  C0
        C13 C12 C11 C10 C9  C8  C7    C6  C5  C4  C3  C2  C1  C0
        |   |   |   |   |   |   | USB |   |   |   |   |   |   |
  R0  --S---S---S---S---S---S---S-----S---S---S---S---S---S---S
        |   |   |   |   |   |   |     |   |   |   |   |   |   |
  R1  --S---S---S---S---S---S---S-----S---S---S---S---S---S---S
        |   |   |   |   |   |   |     |   |   |   |   |   |   |
  R2  --S---S---S---S---S---S---S-----S---S---S---S---S---S---S
        |   |   |   |   |   |   |     |   |   |   |   |   |   |
  R3  --S---S---S---S---S---S---S-----S---S---S---S---S---S---S

```

Here:
* "+" indicates contacting cross
* "C" indicates no contact over cross
* "S" indicates switch and diode connect cross wire combination
* 2 wires for each column

### Key switches and Diodes

```
               COL
                |
    ┌───────────|─┐
  ----------\   | │  /--
    │ +->|---+--^---+
    │ |    O    | │
    │ |         | │
    │ +0        | │
    │           0 │
    └───────────|─┘
```


Please note above schematics are there to understand the thought behind
my wiring design.  The photo provides the most accurate record of my
wiring.

### MCU

Some pin-header are soldered to Blackpill board.  From USB side: P1-P6, P9-P-last (skip 2 pins)
Removals of some pins were needed to avoid interference with case.



