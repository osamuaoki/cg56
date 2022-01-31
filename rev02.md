# cg56: AT90USB1286 (rev02)
<!---
vim:set tw=79 ts=4 sts=4 sw=4 et nosi filetype=markdown:
-->

TEENSY 2.0++ type board with AT90USB1286 offers bigger memory than TEENSY 2.0
type board with ATmega32u4.  When I bought an inexpensive TEENSY 2.0++
compatible board, I decided to switch this project to use it.

## Case Design

LibreCAD was used to create DXF (drawing exchange format) file for case.

* [Keyboard case CAD data](cad/cg56.dxf)

MDF was cut by laser using this data.  Small holes are used to position each
piece using wooen tooth picks.  Some file works were applied to round the edges
and paper plaster was used to fill uneven surfaces.  So the resulting keyboard
case has a smooth shape.

NC LASER cut MDF board pieces were stacked and glued to form the keyboard case
which holds multiple Gateron-made Cherry-type mechanical switches.  Since
TEENSY 2.0++ was slightly bigger than the original target TEENSY 2.0, MDF case
was grooved to create enough space in it.

## Wiring of MCU

The MCU board is placed on the back of the Q-key with its top surface facing
downward with keyboard case in normal orientation.  When wiring MCU with
keyboard case flipped, the MCU board is placed on the back of the Q-key with
its top surface facing upward from the flipped keyboard case.

Top view of Teensy 2.0++ board:

```
                              Q-key
                              MINI
                              USB
                        +---+-----+---+
                    GND |*  |     |  *|Vcc
                    PB7 |*  |     |  *|PB6
                    PD0 |*  |     |  *|PB5
                    PD1 |*  +-----+  *|PB4
                    PD2 |*           @|PB3    1 MISO
                    PD3 |*           @|PB2    4 MOSI
                    PD4 |*           @|PB1    3 SCLK
                    PD5 |*           *|PB0
              (LED) PD6 |*           *|PE7
                    PD7 |*           *|PE6
                    PE0 |*           *|GND
                    PE1 |*           *|AREF
                    PC0 |*           *|PF0
                    PC1 |*           *|PF1
                    PC2 |*           *|PF2
                    PC3 |*           *|PF3
                    PC4 |*           *|PF4
                    PC5 |*           *|PF5
                    PC6 |*           *|PF6
                    PC7 |*   @ @ @   *|PF7
                        +-------------+
                             ^ ^ ^
                             | | |
        VCC 2 ---------------+ | |
        GND 6 -----------------+ |
        RST 5 -------------------+
```
The pin names used here are ones for AT90USB1286 chip.

Here, the Teensy2.0++ board has:
* 43 total PINs (easily accessible outer ones)
* 7 non-IO PINs
* 36 I/O pins  --- This is the advantage too.

There are 31 I/O needs under normal usage:
* 8 rows of keyboard switches (input)
* 7 columns of keyboard switches (output)
* 8 LEDs of keyboard (output)
* 1 internal LED
* 1 Speaker (output)
* 6 dedicated ISP programming (B1, B2, B3, VCC, GND, RST)

This can be implemented as:

* DIODE_DIRECTION COL2ROW (Column as input side)
* ROW[0:7] =      { B7, D0, D1, D2, D3, D4, D5, D7 } (write) - Cathode
* Column[0:6] =   { E0, E1, C0, C1, C2, C3, C4 }     (read) - Anode
* LED[1:4][R:B] = { B6, B5, B4, B0, E7, E6, C5, C7}
* Internal_LED =  { D6 }
* Sound[0] =      { C6 } (OCR3A, Timer 3, channel A, 16 bits)

This ensures access to reprogram via SPI/ISP/ICSP using AVR-ISP II by keeping
all the following pins available as input if keys are not pressed.

* B1 SCLK
* B2 MOSI
* B3 MISO
*    RESET

Also this leaves all PF-pins for analog I/O.

(Reset should have pull-up resister 10K ???)

#### MCU to Parts

For easier wiring, let's recap as a counter clockwise mapping for the MCU
board. (MCU under Q-Key, far side first):

The pin names usage here are ones for AT90USB1286 chip.

Pin order from USB connector side:

* Anti-clock wise: Top view
* Clock wise: Bottom view


| MCU | Parts   | Note                 |I/O|
|-----|---------|----------------------|---|
| GND | GND     | * *                  |   |
| PB7 | R0      | `Q`-row              | O |
| PD0 | R2      | `A`-row              | O |
| PD1 | R4      | `Z`-row              | O |
| PD2 | R6      | left-bottom-row      | O |
| PD3 | R1      | `P`-row              | O |
| PD4 | R3      | `;`-row              | O |
| PD5 | R5      | `/`-bottom row       | O |
| PD6 |         | Internal LED         | O |
| PD7 | R7      | right-bottom row     | O |
| PE0 | C0      | `Tab/]`-column       | I |
| PE1 | C1      | `Q/Y`-column         | I |
| PC0 | C2      | `W/U`-column         | I |
| PC1 | C3      | `E/I`-column         | I |
| PC2 | C4      | `R/O`-column         | I |
| PC3 | C5      | `T/P`-column         | I |
| PC4 | C6      | `[/BS`-column        | I |
| PC5 | LED4R   | `P`-side LED         | O |
| PC6 | SPEAKER | SPEAKER              | O |
| PC7 | LED4B   | `P`-side LED         | O |
| VCC | VCC     | * *                  | * |
| GND | GND     | * *                  | O |
| RST | RST     | * *                  | * |
| PF7 | LED     | `Y`-side LED         | O |
| PF6 | LED     | `Y`-side LED         | O |
| PF5 | LED     | `T`-side LED         | O |
| PF4 | LED     | `T`-side LED         | O |
| PF3 | A?      |                      |   |
| PF2 | A?      |                      |   |
| PF1 | A?      |                      |   |
| PF0 | A?      |                      |   |
| Aref| A?      |                      |   |
| GND |         |                      |   |
| PE6 |         |                      | O |
| PE7 |         |                      | O |
| PB0 |         |                      | O |
| PB1 | SCLK    | * *                  | O |
| PB2 | MOSI    | * *                  | O |
| PB3 | MISO    | * *                  | O |
| PB4 |         |                      | O |
| PB5 | LED1B   | `Q`-side LED         | O |
| PB6 | LED1R   | `Q`-side LED         | O |
| VCC | NC      | * *                  |   |

PF-pins will be used for joystick (optional).

micro-USB (female) on PCB to mini-USB (male) conversion is created by cutting
mini-USB-cable and using micro-USB pitch converter PCB.

USB wire color inside cable:

* RED -- `USB VCC (5V)`
* GREEN -- `D+`
* WHITE -- `D-`
* BLACK -- `GND`

#### MCU firmware

Please note that the generic Teensy 2.0++ bought at Aliexpress/bangood are just
PCB pattern copy product which doesn't come with the proprietary Teensy
firmware by pjrc.com.

First thing to do is flash your AT90USB1286 chip with the LUFA HID bootloader
by connecting the programmer via SPI interface using `avrdude` program.  Any
SPI interface programmer can be used.  The LUFA HID bootloader is a good choice
and takes 4KB space for the bootloader.

Connection to ISP is done with IC clip directly to the board. (no connector)

See https://osamuaoki.github.io/jp/2021/12/11/atmega32u4-4/

### LED

Follow the GH60 LED code example for simplicity for 8 GPIO PINs in which
simple on/off control is applied to the anodes of the LEDs with their
cathodes grounded.  Use CRD (18mA) between Teensy output and Anode of each
LED.  Please note total current limitation limits only 4 of them can be on at
once.

User can redefine one of the following `__attribute__ ((weak))` functions
to customize LED behavior:

* `led_set_user`
* `led_set_kb`
* `led_set`

USB control the following mode LED

* Numeric lock:
* Caps Lock:
* Scroll lock:

### Guide for the wiring steps

1. Test MCU board by programming via AVR-ISP with blink LED on PD6.

1. LEDs are lightly glued with hot-melt glue to the key switches to prevent
   falling.

1. All 56 key switches are placed into the keyboard case.  I used hot-melt glue
   to hold loose switches.  Please don't overdo it.  You can always add more
   glue after everything is completed to ensure the mechanical integrity of the
   keyboard.

1. Then the wiring of rows are done.  Adjacent row pins are connected with 24mm
   cut ETFE wire of AWG30 (0.26mmD) with each end stripped about 2mm.  Wire are
   passed through inside cavity of the case so it doesn't interfere with the
   bottom cap plate of the case.

1. Then the wiring of columns are done.  Line marked anode side of diodes are
   always connected to the key switches.  Cathode side with bar are connected
   within each column. (Column to row configuration)

1. Use tester to check keyboard connections in ohm mode.  (Please note that, in
   ohm mode of modern multimater, diode appears as insulator for both
   directions.)

1. Use tester to check forward and reverse voltage drop across each diode to be
   around 0.5V-0.6V and OL (overrange) to make sure each diode pointing the
   right direction and not shorted.  It is easy to overlook the cathode
   marking.

1. Connect MCU board to parts.

1. Place PI tape underneath the MCU, speaker and connector positions to prevent
   shorting the circuit and mark proper MCU position aligning with the bottom
   cap.  Watch for VCC(5V), output pins and circuit around USB connector.

1. Secure position of MCU with the hot-melt glue to the case.

1. Glue micro-USB female connector on PCB to the case.  (This has a short male
   mini-USB plug soldered)

1. Connect mini-USB connector to MCU board.

1. If keyboard functions perfectly, close the bottom plate.

## BOM

The following reference prices are rough US$ figures for the baseline
estimates.  Expect to drop about $150 which is more than a typical gaming
mechanical keyboards.  It ain't cheap but it's fun!!!

* 1 x Teensy 2.0++ compatible (banggood)  --  $15
* 1 x Mini-USB cable - comes with Teensy2.0
* Lasser cut 3mm MDF board              --  $50 (Japanese local company)
* Gateron Brown 87 switches (ebay)      --  $30
* Blank PBT 87 keycaps (ebay)           --  $20
* LED                                   --  $ 5
* CRD                                   --  $ 5
* Speaker (Piezo Transducer)            --  $ 1
* Diode IN4148 equiv. (100 piece pack)  --  $ 2
* AWG30 multi color set                 --  $ 5
* Hot melt glue and glue gun            --  $10 (?)
* Solders, circuit tester, ...

