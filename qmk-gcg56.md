# QMK for gcg56
<!---
vim:set tw=79 ts=4 sts=4 sw=4 et nosi filetype=markdown:
-->

XXX FIXME XXX


## Firmware Choice

* "Quantum Mechanical Keyboard Firmware" by Jack Humbert et al.
   * https://github.com/qmk/qmk_firmware
     Use GH60 and Plunk as the baseline to start this custom keyboard

## Build environment of QMK firmware

On Debian GNU/Linux system:

```
 $ sudo apt update
 $ sudo apt install devscripts git gitk binutils-avr gcc-avr avr-libc avrdude
 ...
 $ cd ~/path/to
 $ git clone https://github.com/osamuaoki/cg56.git
 $ cd cg56
 $ git subhmodule update --init
 $ cd qmk_firmware/keyboards/cg56
 $ make default
```

You'll be able to generate your .hex as above.  Then, you can then use the
Teensy Loader to program your .hex file.

### Other Keymaps

Several versions of keymaps may be available in advance but you are
recommended to define your favorite layout yourself.

The following will create your own keymap named **__keymapname__** as a
firmware binary hex file:

```
 $ cd ~/path/to/cg56/qmk_firmware/keyboards/cg56/keymaps
 $ cp -a default keymapname
 $ cd keymapname
 $ vim keymap.c
   ... hack hack hack
 $ make keymapname
```

