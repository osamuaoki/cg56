# Key matrix for cg56
<!---
vim:set tw=79 ts=4 sts=4 sw=4 et nosi filetype=markdown:
-->

## Physical and Electrical Key Positions

The electric wiring column and row positions are described as below in
"Column/Row" order in the physical key positions.

Please note:
* Physical matrix: 4 rows * 14 columns
* Electrical matrix: 8 rows * 7 columns

This allows to scan 54 keys only with 15 pins.

## Top view

This top view can be used for software key address configuration.

```
 Q-side (Teensy)                          P-side (Speaker)
 ┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
 │0/0│1/0│2/0│3/0│4/0│5/0│6/0│0/1│1/1│2/1│3/1│4/1│5/1│6/1│
 ├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
 │0/2│1/2│2/2│3/2│4/2│5/2│6/2│0/3│1/3│2/3│3/3│4/3│5/3│6/3│
 ├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
 │0/4│1/4│2/4│3/4│4/4│5/4│6/4│0/5│1/5│2/5│3/5│4/5│5/5│6/5│
 ├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
 │0/6│1/6│2/6│3/6│4/6│5/6│6/6│0/7│1/7│2/7│3/7│4/7│5/7│6/7│
 └───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘
```

This is useful view for writing software for the key input conversion.

#### Bottom view

This bottom view can be used for wiring key switches.

```
 P-side (Speaker)                         Q-side (Teensy)
 ┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
 │6/1│5/1│4/1│3/1│2/1│1/1│0/1│6/0│5/0│4/0│3/0│2/0│1/0│0/0│
 ├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
 │6/3│5/3│4/3│3/3│2/3│1/3│0/3│6/2│5/2│4/2│3/2│2/2│1/2│0/2│
 ├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
 │6/5│5/5│4/5│3/5│2/5│1/5│0/5│6/4│5/4│4/4│3/4│2/4│1/4│0/4│
 ├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
 │6/7│5/7│4/7│3/7│2/7│1/7│0/7│6/6│5/6│4/6│3/6│2/6│1/6│0/6│
 └───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘
```

This is useful view for wiring and building the hardware.

## Wiring of Switches and LEDs

This section always uses bottom view unless otherwise noted.

### Global simplified view of wiring

Here is a global simplified view of wiring of switches and LEDs:

```
   P-side (Speaker)                                  Q-side (Teensy)

    LED4                      LED3  LED2                       LED1
    RCB                        RCB  RCB                         RCB

    C6  C5  C4  C3  C2  C1  C0            C6  C5  C4  C3  C2  C1  C0
    |   |   |   |   |   |   |             |   |   |   |   |   |   |
    S---S---S---S---S---S---S-- R1  R0  --S---S---S---S---S---S---S
    |   |   |   |   |   |   |             |   |   |   |   |   |   |
    S---S---S---S---S---S---S-- R3  R2  --S---S---S---S---S---S---S
    |   |   |   |   |   |   |             |   |   |   |   |   |   |
    S---S---S---S---S---S---S-- R5  R4  --S---S---S---S---S---S---S
    |   |   |   |   |   |   |             |   |   |   |   |   |   |
    S---S---S---S---S---S---S-- R7  R6  --S---S---S---S---S---S---S

```

Here:
* "+" indicates contacting cross
* "C" indicates no contact over cross
* "S" indicates switch and diode connect cross wire combination
* 2 wires for each column

### Key switches, LEDs, CRDs, Diodes and Speaker

Insert [2 color LED](img/OSRB3132A.png) as below to the key switches (Bottom view)

```
 6/1, 0/1 switches          5/1, 4/1, 3/1, 2/1, 5/0, 4,0, 3/0, 2/0, 1/0,
                            6/3, 5/3, ..., 1/3, 6/2, 5/2, ..., 1/2, 0/2,
                            6/5, 5/5, ..., 1/5, 6/4, 5/4, ..., 1/4, 0/4
                            switches
  LED  LED  LED                  COL
  R    C    B                     |
 ┌|────|────|──┐                ┌─|───────────┐
 │0->|-0-|<-0  │                │ |           │
 │             │                │ V           │
 │      O      │                │ -    O      │
 │             │                │ |           │
 │ +0          │                │ +0          │
 │ |         0 │                │           0 │
 └─|─────────|─┘                └───────────|─┘
   -         +-- ROW                        +-- ROW
   ^
   +-- COL

  6/0, 0/0 switches          6/7, 5/7, ..., 1/7, 6/6, 5/6, ..., 1/6, 0/6
  are mirror image           switches are flipped orientation

```

* LEDs are inserted to the select 4 top row key switches only
* RED LED side are inserted to the outside
* 0/0 is mirror image of 6/1
* LED inserting side of key switches are pointing to the far-side
* Bottom row flips direction of key switches for the easier pin access
* [CRD](img/CRD.png) is used to limit current to LED.

Use 4 of 3mm RED-BLUE LED after grinding off the bottom rip of LED-resin.

```
     LED    CRD
  +--|<-----|O--- Driver for Blue (inner side) LED?B
  |      (-)  (+)
  +-------------- GND                          LED?C
  |
  +--|<-----|O--- Driver for Red  (outer side) LED?R
                                               ?=1.2.3.4
```

Always anode side (bars printed) of diodes and CRDs are connected to the
switches and CRDs.

Original case for the speaker was removed to make it fit inside.

### Full view of wiring

Here is the full view of wiring as the bottom view

```
    P-side (Speaker)                                                                                                                                                     Q-side (Teensy)

        LED4                                                                     LED3                LED2                                                                      LED1
        RCB                                                                      RCB                 RCB                                                                       RCB
        |||                                                                      |||                 |||                                                                       |||
        A|A                                                                      A|A                 A|A                                                                       A|A
        |+|----------------------------------------------------------------------|+|--------+--------|+|-----------------------------------------------------------------------|+|
        | |                                                                      | |        |        | |                                                                       | |
                                                                                           GND

    X-----------X-----------X------------X-----------X-----------X-----------X------------+   +----X-----------X-----------X------------X-----------X-----------X-----------X---------
            X           X            X           X           X           X           X    |   |            X           X            X           X           X           X           X
            |           |            |           |           |           |           |    R1  R0           |           |            |           |           |           |           |
        C6  A       C5  A       C4   A       C3  A       C2  A       C1  A       C0  A                 C6  A       C5  A       C4   A       C3  A       C2  A       C1  A           A
        |   |       |   |       |    |       |   |       |   |       |   |       |   |                 |   |       |   |       |    |       |   |       |   |       |   |       |   |
        +---+       +---+       +----+       +---+       +---+       +---+       +---+                 +---+       +---+       +----+       +---+       +---+       +---+       +---+
        |   |       |   |       |    |       |   |       |   |       |   |       |   |                 |   |       |   |       |    |       |   |       |   |       |   |       |   |
        |   V       |   V       |    V       |   V       |   V       |   V       |   V                 |   V       |   V       |    V       |   V       |   V       |   V       |   V
        |   |       |   |       |    |       |   |       |   |       |   |       |   |                 |   |       |   |       |    |       |   |       |   |       |   |       |   |
    X---C---C---X---C---C---X---C----C---X---C---C---X---C---C---X---C---C---X---C---C----+   +----X---C---C---X---C---C---X---C----C---X---C---C---X---C---C---X---C---C---X---C---C-
        |   |       |   |       |    |       |   |       |   |       |   |       |   |    |   |        |   |       |   |       |    |       |   |       |   |       |   |       |   |
        |   X       |   X       |    X       |   X       |   X       |   X       |   X    R3  R2       |   X       |   X       |    X       |   X       |   X       |   X       |   X
        |           |           |            |           |           |           |                     |           |           |            |           |           |           |
        |           |           |            |           |           |           |                     |           |           |            |           |           |           |
        |           |           |            |           |           |           |                     |           |           |            |           |           |           |
    X---C-------X---C-------X---C--------X---C-------X---C-------X---C-------X---C--------+   +----X---C-------X---C-------X---C--------X---C-------X---C-------X---C-------X---C-----
        |   X       |   X       |    X       |   X       |   X       |   X       |   X    |   |        |   X       |   X       |    X       |   X       |   X       |   X       |   X
        |   |       |   |       |    |       |   |       |   |       |   |       |   |    R5  R4       |   |       |   |       |    |       |   |       |   |       |   |       |   |
        |   A       |   A       |    A       |   A       |   A       |   A       |   A                 |   A       |   A       |    A       |   A       |   A       |   A       |   A
        |   |       |   |       |    |       |   |       |   |       |   |       |   |                 |   |       |   |       |    |       |   |       |   |       |   |       |   |
        +---+       +---+       +----+       +---+       +---+       +---+       +---+                 +---+       +---+       +----+       +---+       +---+       +---+       +---+
            |           |            |           |           |           |           |                     |           |            |           |           |           |           |
            V           V            V           V           V           V           V                     V           V            V           V           V           V           V
            |           |            |           |           |           |           |    R7  R6           |           |            |           |           |           |           |
            X           X            X           X           X           X           X    |   |            X           X            X           X           X           X           X
    X-----------X-----------X------------X-----------X-----------X-----------X------------+   +----X-----------X-----------X------------X-----------X-----------X-----------X---------
```

Here:
* "+" indicates contacting cross
* "C" indicates no contact over cross
* "X" indicates switch terminal soldering
* "A" and "V" are meant to be diode (up/down arrow).
* ".." are meant to be long distance wiring

Please note above schematics are there to understand the thought behind
my wiring design.  The photo provides the most accurate record of my
wiring.



