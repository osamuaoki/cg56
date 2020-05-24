# cg56: AT90USB1286 (rev02)
<!---
vim:set tw=79 ts=4 sts=4 sw=4 et nosi filetype=markdown:
-->


## Wiring of MCU

XXX FIXME XXX

MCU used is ATmega32u4 on the generic Teensy compatible board since it offers
better access to the I/O pins (25) than pro-micro boards (20).  The MCU board
is placed on the back of the Q-key with its top surface facing downward with
keyboard case in normal orientation.  When wiring MCU with keyboard case
flipped, the MCU board is placed on the back of the Q-key with its top surface
facing upward from the flipped keyboard case.

Top view of Teensy++ board:


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

Here, the Teensy board has:
* 31 total PINs
* 6 non-IO PINs
* 25 I/O pins  --- This is the advantage over pro-micro board.

There are 25 I/O needs under normal usage:
* 8 rows of keyboard switches (input)
* 7 columns of keyboard switches (output)
* 8 LEDs of keyboard (output)
* 1 internal LED
* 1 Speaker (output)

This can be implemented as:

* DIODE_DIRECTION COL2ROW
* ROW[0:7] =      { B0, B1, B2, B3, E6, B7, D2, D3 } (read)
* Column[0:6] =   { F0, F1, F4, F5, F6, F7, B6 }     (write)
* LED[1:4][R:B] = { D0, D1, C7, C5, D7, D4, B5, B4 }
* Internal_LED =  { D6 }
* Sound[0] =      { C6 } (OCR3A, Timer 3, channel A, 16 bits)

This ensures access to reprogram via SPI/ISP/ICSP by keeping all the
following pins available as input if keys are not pressed.

* B1 SCLK
* B2 MOSI
* B3 MISO
*    RESET

Reset should have pull-up resister 10K

#### MCU to Parts

For easier wiring, let's recap as a counter clockwise mapping for the MCU
board. (MCU under Q-Key, far side first):

The pin names usage here are ones for ATmega32u4 chip.

| MCU | Parts   | Note                 |I/O|
|-----|---------|----------------------|---|
| GND | GND     | * *                  |   |
| PB0 | R0      | `Q`-row              | I |
| PB1 | R1/ICSP | `P`-row    (SCLK)    | I |
| PB2 | R2/ICSP | `A`-row    (MOSI)    | I |
| PB3 | R3/ICSP | `;`-row    (MISO)    | I |
| PE6 | R4      | `Z`-row (inside)     | I |
| PB7 | R5      | `/`-row              | I |
| PD0 | LED1R   | `Q`-side LED (SCL)   | O |
| PD1 | LED1B   | `Q`-side LED (SDA)   | O |
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


#### MCU firmware

Please note that the generic Teensy doesn't come with the proprietary Teensy
firmware.

First thing to do is flush your ATmega32u4 chip with the LUFA HID bootloader by
connecting the programmer via SPI interface using `avrdude` program.  Any SPI
interface programmer can be used.  Although the real Teensy comes with 512B
bootloader, the LUFA HID bootloader is slightly larger than 2048B and requires
you to keep 4KB space for the bootloader.  There is an alternative Free small
HID bootloader [NanoBoot](https://github.com/volium/nanoBoot) with 512B.  You
may try it too.

Arduino ICSP port (Top view of male MCU side connector):
```
1 MISO  x x  VCC
3 SCLK  x x  MOSI
5 RESET x x  GND
```

### LED

Follow the GH60 LED code example for simplicity for 8 GPIO PINs in which
simple on/off control is applied to the anodes of the LEDs with their
cathodes grounded.  Use CRD (18mA) between Teensy output and Anode of each
LED.

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

1. LEDs are lightly glued with hot-melt glue to the key switches to prevent
   falling.

1. All 56 key switches are placed into the keyboard case.  I used hot-melt glue
   to hold loose switches.  Please don't overdo it.  You can always add more
   glue after everything is completed to ensure the mechanical integrity of the
   keyboard.

1. Then the wiring of rows are done.  Adjacent row pins are connected with 24mm
   cut PTFE wire of AWG30 (0.26mmD) with each end stripped about 2mm.  Wire are
   passed through inside cavity of the case so it doesn't interfere with the
   bottom cap plate of the case.

1. Then the wiring of columns are done.  Line marked anode side of diodes are
   always connected to the key switches.  Cathode side with bar are connected
   within each column.

1. Use tester to check keyboard connections and check insulation with reverse
   bias for each diode.  Since diodes needs to be squeezed into tight space,
   there is a fairly good change of shorting unexpectedly.

1. 3 rows (PA;) are connected to male 2x6 pins which plugs into the Arduino
   ICSP port.

1. ICSP port (Female) is connected to MCU board  PB1, PB2, PB3, RST, GND, and
   VCC.

1. Test MCU board by programming via ICSP with blink LED on PD6.

1. LEDs are connected to the MCU board using long wire.  D0 and D1 should have
   Dupont connectors.  Others, if possible.

1. Test LED connections by programming via ICSP with blink LED.

1. Speaker is connected to the MCU board using long wire with Dupont
   connectors.

1. Test speaker connections by programming via ICSP with beep.

1. Then other rows and columns are connected to the MCU board using long wires.
   Consider inserting Dupont connectors as much as possible.  Please note the
   column wiring is passed through the surface of the case since it doesn't
   interfere with the bottom cap plate of the case.  All side way connections
   are made via the top row where bottom cover plate was groove.  Please note
   ROW wires connect to the far-side of the MCU board.

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

