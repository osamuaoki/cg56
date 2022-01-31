# gcg56: STM32F411CEU6 (rev03)
<!---
vim:set tw=79 ts=4 sts=4 sw=4 et nosi filetype=markdown:
-->

ARM based Blackpill board with STM32F411CEU6 offers bigger memory than AVR
based TEENSY 2.0 and TEENSY 2.0++ boards.

## Case Design

This is an alternative case design for a custom ortholinear keyboard case which
doesn't rely on accessibility to the high precision NC laser-cut nor milling
tools.  This uses commodity 5x5 mm square plastic rods, glue, rasp (file), and
the hand saw. (I use acrylic rods but ABS or hardwood bar should work too.)

The base size of
KB-SW is about 14.5 x 14 mm and 5mm rods work nicely to realize the standard 19
mm KB pitch.

### Step 1:
Cut 5mmx5 mm rods into following length (mm) with the hand saw:

```
TOP
 size    pcs
  295      2 (outside)
   75     14
   12     42

BTM
 size    pcs
  285      2 (outside)
  285      2 (inside) -- make groove for wire paths
   85      2 (outside)

PWB gap ------  18 (~ 7 pitch)  -- this was for Teansy 2.0++

BTM Plate -- ~ 290 x 80 mm

```
Here, theoretically center gap for PWB should be 19 mm (`= 295 - 14 * 19 - 10 =
276, 295 - 276`) if horizontal key spacing is exactly 19mm.  Actual center gap
is 18mm (1mm lost in extra spaces consumed over 14 key switches).  18mm is
enough for Teensy 2.0 ++ PCB (2.54x7=17.78 mm wide) to fit inside but blackpill
is wider (2.54x8=20.32 mm wide).

Also, vertical spacing is 20mm (`=(75+5)/4`mm).  These extra spaces are needed
to absorb machining variations of the sloppy manual assembly.

Blackpill PCB seems to be 2.54mm wider than Teansy 2.0++ but PIN gaps are at
the same.  This makes Blackpill PCB to sit on case bars while PINs are snug
between bars.

(Please make sure to keep the length of rods as exact as possible.)

### Step 2:

Glue BTM square 285-83-285-83 (solvent) while holding them to flat surface
with masking tapes.  Exact shape is essential.

### Step 3:

Glue TOP square 73-295-73 (solvent) while stacking exactly onto the BTM
square.  Keep 295 temporary fixed by tape.  Grooves are all facing inward of
the square.)

### Step 4:

Set up some KB-SWs using 75s as spacer and glue 75s (solvent type)
After these, glue the non-glued 295.

### Step 5:
Place all KB-SWs, tape fix them, and flip.  Glue 12s as spacer and glue them
(solvent type)

### Step 6:

Place 7s (6-8 pics) at some cross points in side way and glue (solvent type).
(Leave some space for LED back light)

### Step 7:
Place PCB with socket and joy stick on center back and glue. (solvent type)

### Step 8:

Glue KB-SWs (hot melt type) for stability (remove tape)

### Step 9:

Round the bottom and side edges using rasp.

## Step 10:

Add pin header to blackpill (with 2 pins removed from each side to avoid
mechanical interference.)

## Step 11:

Wire! and program!

### Step 99:

Use thin double sided tape to close bottom with plate with leather glued on it.

## Wiring of MCU

The MCU board is placed at the center of the key matrix.

Top view of Blackpill board:

```
Q-side                       USB-C                    P-side
                        +---+-----+---+
                25  B12 |*  |     |  *| 5V
                26  B13 |*  |     |  *| GND
                27  B14 |*  |     |  *| 3V3
                28  B15 |*  +-----+  *| B10 21
                29  A8  |*           @| B2  20   BOOT1
                30  A9  |*  B     N  @| B1  29   ADC9
XX              31  A10 |*  O     R  @| B0  18
XX              32  A11 |*  O     S  @| A7  17
                33  A12 |*  T     T  *| A6  16
                38  A15 |*  0        *| A5  15
                39  B3  |*           *| A4  14
                40  B4  |*           *| A3  13
                41  B5  |*           *| A2  12
                42  B6  |*           *| A1  11
                43  B7  |*           *| A3  10   ADC0
                45  B8  |*  P        *| R    7   NRST
                46  B9  |*  W        *| C15  4
                    5V  |*  R        *| C14  3
                    GND |*           *| C13  2   LED(Blue)
                    3V3 |*  @ @ @ @  *| VB   1
                        +-------------+
                            ^ ^ ^ ^
                            | | | |
        3V3   --------------+ | | |
    34  DIO   ----------------+ | |
    37  SCK   ------------------+ |
        GND   --------------------+

BOOT1: pull down to ground to enable DFU mode. (10K?)

```
The pin names used here are ones for STM32F411CEU6 chip.

Here, the Blackpill board has:
* 44 total PINs
* 11 non-IO PINs
* 33 digital I/O pins
* 10 analog 12bit DAC pins

There are 31 I/O needs under normal usage:
* 4 rows of keyboard switches (input)
* 14 columns of keyboard switches (output)

This can be implemented as:

* DIODE_DIRECTION COL2ROW (Column as input side)
* ROW[0:3] =      { B12, B13, B14, B15 }      (write) - Cathode
* Column[0:13] =  { A8, A9, A12, A15, B3, B4, B5, B6, B7, B8, B9, C14, C15, B10 }     (read) - Anode

#### MCU to Parts

For easier wiring, let's recap as a counter clockwise mapping for the MCU
board. (MCU at the center:

The pin names usage here are ones for STM32F411CEU6 chip.

Pin order from USB connector side:

* Anti-clock wise: Top view
* Clock wise: Bottom view


| MCU | Parts   | Note               |I/O|
|-----|---------|--------------------|---|
| B12 | R0      | `Q`-row            | O | Q-side
| B13 | R1      | `A`-row            | O |
| B14 | R2      | `Z`-row            | O |
| B15 | R3      | bottom row         | O |
| A8  | C0      | `Tab`-column       | I |
| A9  | C1      | `Q`-column         | I |
| A10 |         | NO PIN             |   |
| A11 |         | NO PIN             |   |
| A12 | C2      | `W`-column         | I |
| A15 | C3      | `E`-column         | I |
| B3  | C4      | `R`-column         | I |
| B4  | C5      | `T`-column         | I |
| B5  | C6      | `[`-column         | I |
| B6  | C7      | `]`-column         | I |
| B7  | C8      | `Y`-column         | I |
| B8  | C9      | `U`-column         | I |
| B9  | C10     | `I`-column         | I |
| 5V  |         |                    |   |
| GND |         |                    |   |
| 3V3 |         |                    |   |
| 3V3 |         | Edge * *           |   |
| DIO |         | Edge * *           |   |
| SCK |         | Edge * *           |   |
| GND |         | Edge * *           |   |
| VB  |         |                    |   |
| C13 |         | internal LED       | O |
| C14 | C11     | `O`-column         | I |
| C15 | C12     | `P`-column         | I |
| RST | NRST    | * *                | * |
| A3  | ADC0    | (Joystick)         |   |
| A4  | ADC1    | (Joystick)         |   |
| A5  | ADC2    |                    |   |
| A6  | ADC3    |                    |   |
| A7  | ADC4    |                    |   |
| A8  | ADC5    |                    |   |
| A9  | ADC6    |                    |   |
| A10 | ADC7    | NO PIN             |   |
| B0  | ADC8    | NO PIN             |   |
| B1  | ADC9    |                    |   |
| B2  | BOOT1   | (10K to GND)       |   |
| B10 | C13     | `BS`-column        | I |
| 3V3 | VCC     | * *                | * |
| GND | GND     | * *                | O |
| 5V  | NC      | * *                |   |P-side

ADC-pins will be used for joystick (optional).

#### MCU firmware

Use DFU

### Guide for the wiring steps

1. Test MCU board by programming via DFU mode with blink LED on PD6 using
   dfu-util.

1. All 56 key switches are placed into the keyboard case.  I used hot-melt glue
   to hold loose switches.  Please don't overdo it.  You can always add more
   glue after everything is completed to ensure the mechanical integrity of the
   keyboard.

1. Then the wiring of rows are done using anode-side lead wires of diode with
   each cathode-side lead wire connected to each switch.  The right and left
   side of anode-side leads are connected.  This makes total of 4 AWG30 wires
   for the row signal. (Column to row configuration)

1. Then the wiring of columns are done using short UEW AWG32 wires.  One AWG30
   wire is soldered for each column.  This makes total of 14 AWG30 wires for
   the column signal.

1. Use tester to check keyboard connections in ohm mode.  (Please note that, in
   ohm mode of modern multimater, diode appears as insulator for both
   directions.)

1. Use tester to check forward and reverse voltage drop across each diode to be
   around 0.5V-0.6V and OL (over-range) to make sure each diode pointing the
   right direction and not shorted.  It is easy to overlook the cathode
   marking.

1. Secure position of MCU with the hot-melt glue to the case.

1. Connect MCU board to parts with wire-wrapping.

1. Connect USB-C connector to MCU board.

## BOM

The following reference prices are rough US$ figures for the baseline
estimates.  Expect to drop about $150 which is more than a typical gaming
mechanical keyboards.  It ain't cheap but it's fun!!!

* 1 x Blackpill                         --  $7
* 1 x USB-C cable
* Gateron Brown+white switches          --  $30
* Blank PBT 87 keycaps (ebay)           --  $20
* Diode IN4148 equiv. (100 piece pack)  --  $ 2
* AWG30 multi color set                 --  $ 5
* UEW AWG32 wire                        --  $ 5?
* Hot melt glue and glue gun            --  $10 (?)
* Solders, circuit tester, ...

