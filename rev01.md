# cg56: ATmega32u4 (rev01)
<!---
vim:set tw=79 ts=4 sts=4 sw=4 et nosi filetype=markdown:
-->

WARNINGS: This had design flaw of combination of the diode direction and
pins used for ISP connection.

NOTE:  This was never made at this moment.  This is left as is to record what I
was thinking initially.

## Case Design

LibreCAD was used to create DXF (drawing exchange format) file for case.

* [Keyboard case CAD data](cad/cg56.dxf)

MDF was cut by laser using this data.  Small holes are used to position each
piece using wooen tooth picks.  Some file works were applied to round the edges
and paper plaster was used to fill uneven surfaces.  So the resulting keyboard
case has a smooth shape.

## Wiring of MCU

Initially, ATmega32u4 was considered as the MCU used for this USB keyboard
project.  Generic Teensy 2.0 compatible board was selected since it offers
better access to the I/O pins (25) than pro-micro boards (20).

NC LASER cut MDF board pieces were stacked and glued to form the keyboard case
which holds multiple Gateron-made Cherry-type mechanical switches.

The MCU board is placed on the back of the Q-key with its top surface facing
downward with keyboard case in normal orientation.  When wiring MCU with
keyboard case flipped, the MCU board is placed on the back of the Q-key with
its top surface facing upward from the flipped keyboard case.

Top view of Teensy 2.0 board:


```
                              Q-key
                              MINI
                              USB
                        +---+-----+---+
Interior            GND |*  |     |  *|Vcc              Interior
PE6         SS      PB0 |*  |     |  *|PF0  ADC0        AREF
AIN0        SCLK    PB1 |*  |     |  *|PF1  ADC1          |
INT6        MOSI    PB2 |*  +-----+  *|PF4  ADC4          |
  +->       MISO    PB3 |*  *     *  *|PF5  ADC5        <-+
RTS OC1C    OC0A    PB7 |*           *|PF6  ADC6
SCL OC0B    INT0    PD0 |*           *|PF7  ADC7
SDA         INT1    PD1 |*           *|PB6  ADC13   OC1B    OC4B
RXD1        INT2    PD2 |*           *|PB5  ADC12   OC1A    ~OC4B
TXD1        INT3    PD3 |*           *|PB4  ADC11
~OC4A       OC3A    PC6 |*           *|PD7  ADC10   T0      OC4D
OC4A        ICP3    PC7 |* * * * * * *|PD6  ADC9    T1      ~OC4D  (LED)
                        +-------------+
                           ^ ^ ^ ^ ^
CTS         XCK1    PD5 ---+ | | | +---PD4  ADC8    ICP1
VCC -------------------------+ | |
GND ---------------------------+ |
RST -----------------------------+
```
The pin names used here are ones for ATmega32u4 chip.

Here, the Teensy 2.0 board has:
* 31 total PINs (12+5+2+12) internal one is hard to reach
* 6 non-IO PINs
* 25 I/O pins  --- This is the advantage over pro-micro board.

There are 25 I/O needs under normal usage:
* 8 rows of keyboard switches (input)
* 7 columns of keyboard switches (output)
* 8 LEDs of keyboard (output)
* 1 internal LED
* 1 Speaker (output)

This can be implemented as:

* DIODE_DIRECTION COL2ROW (Column as input side)
* ROW[0:7] =      { B0, B1, B2, B3, E6, B7, D2, D3 } (write) - Kathode
* Column[0:6] =   { F0, F1, F4, F5, F6, F7, B6 }     (read) - Anode
* LED[1:4][R:B] = { D0, D1, C7, C5, D7, D4, B5, B4 }
* Internal_LED =  { D6 }
* Sound[0] =      { C6 } (OCR3A, Timer 3, channel A, 16 bits)

This ensures access to reprogram via SPI/ISP/ICSP using AVR-ISP II by keeping
all the following pins available as input if keys are not pressed.

* B1 SCLK
* B2 MOSI
* B3 MISO
*    RESET

(Oops, I think diode direction should have been ROW2COL to make B1-B3 to be
input (read) side, otherwise programming ISP mode comes with some risk.  Sigh.
Anyway, I didn't make this)

Reset should have pull-up resister 10K

#### MCU to Parts

For easier wiring, let's recap as a counter clockwise mapping for the MCU
board. (MCU under Q-Key, far side first):

The pin names usage here are ones for ATmega32u4 chip (Diode is assumed to be
ROW2COL).

| MCU | Parts   | Note                 |I/O|
|-----|---------|----------------------|---|
| GND | GND     | * *                  |   |
| PB0 | R0      | `Q`-row              | I |
| PB1 | R1/ICSP | `P`-row    (SCLK)    | I |
| PB2 | R2/ICSP | `A`-row    (MOSI)    | I |
| PB3 | R3/ICSP | `;`-row    (MISO)    | I |
| PE6 | R4      | `Z`-row (inside)     | I |
| PB7 | R5      | `/`-row              | I |
| PD0 | LED4R   | `P`-side LED         | O |
| PD1 | LED4B   | `P`-side LED         | O |
| PD2 | R6      | left-bottom row      | I |
| PD3 | R7      | right-bottom row     | I |
| PC6 | SPEAKER | SPEAKER              | O |
| PC7 | LED3R   | `Y`-side LED         | O |
| PD5 | LED3B   | `Y`-side LED         | O |
| VCC | ICSP    | * *                  | * |
| GND | LED/ICSP| * *                  | O |
| RST | ICSP    | * *                  | * |
| PD4 | LED2B   | `T`-side LED         | O |
| PD6 | Int_LED | * * (internal LED)   | O |
| PD7 | LED2R   | `T`-side LED         | O |
| PB4 | LED1B   | `Q`-side LED         | O |
| PB5 | LED1R   | `Q`-side LED         | O |
| PB6 | C6      | `Tab/]`-column       | O |
| PF7 | C5      | `Q/Y`-column         | O |
| PF6 | C4      | `W/U`-column         | O |
| PF5 | C3      | `E/I`-column         | O |
| PF4 | C2      | `R/O`-column         | O |
| PF1 | C1      | `T/P`-column         | O |
| PF0 | C0      | `[/BS`-column        | O |
| VCC | NC      | * *                  |   |

This is rather tight and bad design for ISP programming access due to I?O
conflict of actual switch array being COL2ROW.

#### MCU firmware

Please note that the generic Teensy 2.0 bought at Aliexpress are just PCB
pattern copy product which doesn't come with the proprietary Teensy firmware by
pjrc.com.

First thing to do is flash your ATmega32u4 chip with an alternative Free small
HID bootloader [NanoBoot](https://github.com/volium/nanoBoot) with 512B by
connecting the programmer via SPI interface using `avrdude` program.

Connection to ISP is done with IC clip directly to the board. (no connector)

See https://osamuaoki.github.io/jp/2021/12/11/atmega32u4-4/

### LED

Follow the GH60 LED code example for simplicity for 8 GPIO PINs in which simple
on/off control is applied to the anodes of the LEDs with their cathodes
grounded.  Use CRD (18mA) between Teensy 2.0 output and Anode of each LED.

You shouldn't turn on more than 4 LEDs to avid violating maximum total current
for ATmega32u4.

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
   within each column.

1. Use tester to check keyboard connections and check insulation with reverse
   bias for each diode.  Since diodes needs to be squeezed into tight space,
   there is a fairly good change of shorting unexpectedly.

1. Connect MCU board to parts.

1. Place PI tape underneath the MCU, speaker and connector positions to prevent
   shorting the circuit and mark proper MCU position aligning with the bottom
   cap.  Watch for VCC(5V), output pins and circuit around USB connector.

1. Secure position of MCU with the hot-melt glue to the case.

1. Secure mini USB-wire the hot-melt glue to the case.

1. If keyboard functions perfectly, close the bottom plate.

## BOM

The following reference prices are rough US$ figures for the baseline
estimates.  Expect to drop about $150 which is more than a typical gaming
mechanical keyboards.  It ain't cheap but it's fun!!!

* 1 x Teensy 2.0 compatible (banggood)  --  $15
* 1 x Mini-USB cable - comes with Teensy
* Lasser cut 3mm MDF board              --  $50 (Japanese local company)
* Gateron Brown 87 switches (ebay)      --  $30
* Blank PBT 87 keycaps (ebay)           --  $20
* LED                                   --  $ 5
* CRD                                   --  $ 5
* Speaker (Piezo Transducer)            --  $ 1
* Diode IN4148 equiv. (100 piece pack)  --  $ 2
* AWG30 multi color set                 --  $ 5
* Hot melt glue and glue gun            --  $10 (?)
* AWG30 Wires, solders, circuit tester, ...

