title: Teensy from Absolute Scratch Idea

I recently ordered an interesting microcontroller, namely a
[Teensy 3.2 from PJRC](https://www.pjrc.com/store/teensy32.html).
I thought this MCU (microcontroller unit) could be a nice platform to try and
completely understand. I found the development process to be a bit
dissatisfying, especially for figuring out what actually happens on the chip.
Namely, how exactly does the blinking LED program work.

Teensy works with Arduino's development tools, which works well for anyone that
wants to just get up and programming easily. However, that's not what I wanted
to do. The example blinking LED program is about as short as you would hope,
but that hides a lot of what I wanted to know. There's got to at least be some
initialization.

First step was to try and use standard CLI tools instead of the Arduino GUI.
I thought this would be an easy first step and I can just work backwards using
all the normal tools I'm use to. I got all the `arm-none-eabi` packages and was
ready to go. This doesn't seem to be well supported, as there's no official
examples anywhere I could find. I found some third-party repositories, but they
all seemed backwards. First, they go through all the trouble of making an ELF
executable then extract the assembly into a HEX file. Fortunately there is an
official CLI tool, `teensy_loader_cli`, that actually uploads this HEX file to
the Teensy.

I decided to use this as my starting point: the given HEX file, microcontroller
manual, and ARM Cortex manual. The HEX file is just a binary dump encoded into
ASCII. It's 14324 bytes directly mapped to `[0x00000000, 0x000037F4)`, the
flash storage. I thought embedded programs were supposed to be small! Does
blinking an LED really need this much code?

Now I can figure out what actually happens first. I started digging into the
manuals now knowing what's on the flash storage. The microcontroller starts
execution by reading an address from a known location and setting the program
counter to the value at the address. The first instruction actually prepares the
microcontroller to unlock the watchdog (based off the special values looked up
in the manual), that certainly wasn't in the blinking LED source code!

There also seems to be some artifacts from extracting instructions from an ELF
file intended to be linked with C. It preserves 2 registers by pushing them onto
the stack, even though there can't be a caller to this code, a return
instruction wouldn't make sense here. So that's 2 stack positions filled with
garbage in a very constrained microcontroller, more waste!

I'm not sure what this will lead to, but I think it's interesting. Hopefully
someone else does too.
