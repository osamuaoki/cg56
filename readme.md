# Grid Keyboard with 56 keys (cg56 = cgc56 and cgg56)
<!---
vim:set tw=79 ts=4 sts=4 sw=4 et nosi filetype=markdown:
-->

I used to call cg56 (Cartesian Grid Keyboard with 56 keys).  But I renamed
their qmk names by adding a character to differentiate variants.  The github
repository name of this hardware design memo stays "cg56".

* cgc56: Cartesian Grid Keyboard (compact) with 56 keys (2022/Feb)
    * NC laser cut MDF board
    * AT90USB1286
    * ![cgc56 front](img/cgc56-front.jpg)
    * ![cgc56 side](img/cgc56-side.jpg)

* cgg56: Cartesian Grid Keyboard (gap) with 56 keys
    * Hand cut and glued plastic bars.
    * STM32F411CE
    * gcg56 : NO_PHOTO

## Background

I felt pain between my right ring and pinkie fingers.  I wondered why I suffer
such a pain since I type very slowly and my slow typing should be very low
stress on my fingers.  I then realized that I stretch my pinkie finger to reach
"Enter" and "BS" keys which are away from the home position of my right pinkie
finger.  Situation with laptop JIS (JA) keyboard which I now use regularly is
worse than ANSI (US) keyboard since the "Enter" key is 3 steps away.

Even on the ANSI (US) 104 keyboard, my weak right pinkie finger is covering 20
keys while even my capable right pointer finger covers only 9 keys and most
other fingers cover less.  Although these extra keys typed by my right pinkie
finger are mostly rarely typed keys, these are used more for coding programs.

Also, un-natural staggered key arrangement is another annoyance to twist my
body.  The default positions of hands seem to be too close to each other for me
to keep my good posture.

Hmmm... I need to keep my posture right while keeping my fingers relaxed.

## Design Policy

The following design policy is deployed to design a custom keyboard.

* Use only FREE software (DFSG compliant)
* Compact, simple, and natural design
    * Compact enough to be portable: 56 keys (4 rows x 14 columns)
    * Keys in the linear orthogonal Cartesian grid coordinate positions
    * Never move pinkie fingers more than one column away.
    * Never move pointer fingers more than two column away.
    * Keep left and right fingers separated enough.
    * All ASCII codes are accessible without moving the palm position.
* Least leaning pain key-position design
    * Minimal key-position change from the standard ANSI (US).
    * BS at the right top corner.
    * ESC in place of CapsLock position (yay, Vim).
    * Keep F and J keys separated with 4 keys instead of standard 2 keys.
    * Move non-shift non-alphabet symbols to the center.
    * Fn keys in thumb home positions to offer practically all 104 keys.
        * Function keys are in the top row with Fn.
        * Number keys are in the high-middle row with Fn.
        * Odd keys are in the low-middle row with Fn
    * SPACE should be at the center of the bottom row.
    * Cursor keys are in the hard to reach part of the bottom row.
    * Topography effects using key cap height differences with the OEM key set.
* Keep it simple and cheap (It's my first custom keyboard!)
    * Use commodity electronics parts (as much)
    * Use the laser cut MDF board as the case (if needed)
    * Use only 1U key caps from the OEM key cap set (=no stabilizer)
    * No PCB but lots of messy hand wiring :-)

## What's different

You may wonder what's different from other so called "ortholinear" keyboards.

* Less stress to pinkie fingers.
* Enough distance between left and right hands. (2U->4U)
* 7 columns per one side instead of 6 columns such as
  [plunk](https://olkb.com/planck).
* Single piece compact design without irregular shape vs.
  [Ergo42](https://bit-trade-one.co.jp/selfmadekb/adskb42/) and
  [ErgoTravel](https://github.com/jpconstantineau/ErgoTravel) .
* Extensive use of hot-melt glue which should also function as noise dumper.

Yah, it's not much different in basic hardware design ... it's mostly a taste
difference.

## Design thoughts

Here, ASCII art versions of basic design implementation ideas are provided.

### Case Design

Case with orthogonal key switch matrix design with enough space for thumb
finger coverage and without stressing pinkie finger.

* Primary positions: `###` (home positions)
* Almost primary positions: `.#.` (almost home positions)
* Adjacent positions: `...` (easy to reach minimum movement positions)

```
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
│   │...│...│...│...│...│   │   │...│...│...│...│...│   │
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│...│###│###│###│###│...│...│...│...│###│###│###│###│...│
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│...│.#.│...│...│.#.│...│...│...│...│.#.│...│...│.#.│...│
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│...│...│   │   │...│###│...│...│###│...│   │   │...│...│
└───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘
```

### Key Assignment

Use least surprise design based on the standard QWERTY-keyboard.

Here is an example of key assignment.

Initial thought:
```
default
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
│TAB│ q │ w │ e │ r │ t │ [ │ ] │ y │ u │ i │ o │ p │BS │
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│ESC│ a │ s │ d │ f │ g │ ` │ \ │ h │ j │ k │ l │ ; │ ' │
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│Sft│ z │ x │ c │ v │ b │***│***│ n │ m │ , │ . │ / │Sft│
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│Ctr│Alt│ ↑ │ ↓ │Gui│Fn │Spc│Ent│Fn │App│ ← │ → │Alt│Ctr│
└───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘


Fn-pressed
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
│Prt│F1 │F2 │F3 │F4 │F5 │F11│F12│F6 │F7 │F8 │F9 │F10│SL │
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│Cap│ 1 │ 2 │ 3 │ 4 │ 5 │ - │ = │ 6 │ 7 │ 8 │ 9 │ 0 │PB │
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│___│***│***│***│***│***│***│***│***│***│***│***│***│___│
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│___│___│Hom│End│Alt│___│Ins│Del│___│Ctl│PgU│PgD│___│___│
└───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘
```

[Current QMK setting: (2022/Feb updating and
changing)](https://github.com/osamuaoki/qmk_firmware/blob/osamu1/keyboards/cgc56/keymaps/default/keymap.c)
 -- with many tap-hold dual keys (e.g. 5 keys for space)
```
Normal
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
│Esc│ Q │ W │ E │ R │ T │ [ │ ] │ Y │ U │ I │ O │ P │BS │
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│Tab│ A │ S │ D │ F │ G │ ` │ \ │ H │ J │ K │ L │ ; │Ent│
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│Sft│ Z │ X │ C │ V │ B │ _ │ ' │ N │ M │ , │ . │ ↑ │ / │
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│Ctr│Alt│Ins│Gui│Sft│FnU│Spc│Alt│FnD│Crl│Agr│ ← │ ↓ │ → │
└───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘

One of Raise or Lower
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
│Esc│F1 │F2 │F3 │F4 │F5 │F11│F12│F6 │F7 │F8 │F9 │F10│DEL│
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│n# │ 1 │ 2 │ 3 │ 4 │ 5 │ - │ = │ 6 │ 7 │ 8 │ 9 │ 0 │n\ │
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│___│ ! │ @ │ # │ $ │ % │ _ │ + │ ^ │ & │ * │Ins│PgU│Del│
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│___│___│___│___│___│___│XXX│___│VVV│___│___│Hom│PgD│End│
└───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘

Both Raise and Lower
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
│XXX│___│___│___│___│___│Nlk│Slk│Pau│Psc│App│In5│In4│XXX│
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│___│P1 │P2 │P3 │P4 │P5 │ ( │ ) │P6 │P7 │P8 │P9 │P0 │PEn│
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│___│P/ │P* │P- │P+ │P= │ { │ } │Cnf│Mou│XXX│P. │___│XXX│
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│___│___│___│___│___│VVV│___│___│VVV│___│___│___│___│___│
└───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘

```
Please note:

* "shift-pressed" causes the normal shift conversion
* Missing number row keys are generated by Fn-pressed A-row keys.
* Missing function keys are generated by Fn-pressed Q-row keys.
* All `*` keys are user customizable and can be assigned to any valid USB keyboard code.
* Let's worry about details later when we write QMK firmware.
* Actual key layout will be experimentally determined.

### Keyboard Topography (Type 1)

OEM 1U Key cap are used as below to offer optimal topography.


```
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
│*1 │ Q │ Q │ Q │ Q │ Q │*1 │ 1*│ Q │ Q │ Q │ Q │ Q │ 1*│
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│ 1 │_A_│_A_│_A_│_A_│ A │ 1 │ 1 │ A │_A_│_A_│_A_│_A_│ 1 │
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│ Z │ Z │ Z │ Z │ Z │ Z │ 1 │ 1 │ Z │ Z │ Z │ Z │ Z │ Z │
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤
│ A │ Z │ R │ R │ 1 │_Q_│ 1 │ 1 │_Q_│ 1 │ R │ R │ Z │ A │
└───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘
 1:   1-row type
 Q:   Q-row type
 A:   A-row type
 Z:   Z-row type
 R:   Arrow-keys inserted upside down
 _ _: home position
 *:   LED position (outside is RED LED)
```

With the height difference to the adjacent keys, thumb and pinkie fingers
should have easier time to feel its home position.  (I may change height
combination later.)

Also by deploying higher keys (1-row type) for far-away keys, you can type
these keys by pressing their side faces.  They may not be bigger but are
easy to be reached.

### Keyboard Topography (Type 2)

Alternatively, DSA key tops may be used.  For that, modifying some keys with
soft UV-resin or crazy-glue+baking-soda may be interesting options to create
topography.  This is my plan-B.


## Design implementations

### ATmega32u4 (rev01)

Initially, ATmega32u4 on the generic Teensy 2.0 compatible board was considered
as the choice for MCU since it offers better access to the I/O pins (25) than
pro-micro boards (20) and cheap.

* See [ATmega32u4 (rev01)](rev01.md) -- never made -- for details of around MCU and shape of key matrix.
* See [Key matrix for cg56](cg56.md) for electrical details of key matrix.

Never completed!

### AT90USB1286 (rev02)

After making the physical keyboard shell box in 2018 for rev01, I
procrastinated to finish this project for about 2 years without doing anything.

Then AT90USB1286 on the generic Teensy 2.0++ compatible board became available
in very reasonable price point and I got 2 of them.  So I decided to switch
this project to use this MCU which offers higher IO pin counts and larger
memory size.  (rev01 project was stalled.)

In order to fit this larger board, the internal cavity of the physical keyboard
shell box was grounded to make some space by brute force.

I also got micro-USB female connector on pitch conversion PCB board.  I
manually enlarged the cable connection opening to fit it.  This enabled USB
cable connection of this keyboard to become a detachable magnet type.

* See [AT90USB1286 (rev02)](rev02.md) for details of around MCU and shape of key matrix.
* See [Key matrix for cgc56](cgc56.md) for electrical details of key matrix.
* See [cgc56 in Osamu's QMK fork with osamu1 branch](https://github.com/osamuaoki/qmk_firmware)

As you see actual key arrangement is changing .... but it is working nicely
now.  Sound is TODO but LED are done.

### STM32F411 (rev03)

While modifying NC-MDF case manually, I realized I could use commodity 5mmx5mm
plastic bars and glue to make a similar case.

MCU board is placed in the gap of key switches at the center so everything was
flat and easy to construct.

Since powerful ARM board blackpill with similar form factor was available, I
decided to use it.

In order to avoid dead space caused by placing MCU board under the key
switches, I decided to make gap at the center of key matrix and place MCU board
in the gap.  **gcg56** stands for **Gapped** variant of **cg56**.

* See [STM32F411CEU6 (rev03)](rev03.md) for details of around MCU and shape of key matrix.
* See [Key matrix for gcg56](cgg56.md) for electrical details of key matrix.

### Firmware

* See [QMK for cgc56](qmk-cgc56.md)
* See [QMK for cgg56](qmk-cgg56.md)

### Photos

* See [photo for cgc56](photo-cgc56.md)
* See [photo for cgg56](photo-cgg56.md)

## Side project -- QWFRTY layout

**QWFRTY** layout was created as a minimal learning stress alternative layout.

* **QWFRTY** moves only 11-keys from the standard QWERTY.  Almost all short-cuts stay in place.
* **QWFRTY** encourages alternating hand typing like DVORAK by having all vowel characters on one side.  This should be lower stress.
  **QWFRTY** doesn't trash the user experience under Vim.  `hjkl`-keys are clustered as inverse-T making it even better than QWFRTY.

![QWFRTY](/img/QWFRTY-simple.png)

See https://osamuaoki.github.io/jp/2022/02/14/qwfrty/


