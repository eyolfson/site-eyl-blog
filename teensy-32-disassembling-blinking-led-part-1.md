title: Teensy 3.2, Disassembling Blinking LED, Part 1

The [teensy_loader_cli](https://github.com/PaulStoffregen/teensy_loader_cli/)
repository provides the example blinking LED example I alluded to in the earlier
post. The exact file is `blink_slow_Teensy32.hex`. I've been doubting all 14324
bytes are required and thought embedded programming was a bit better.

The first two addresses in the vector table are all we need to get started. The
first address at `0x00000000` is the initial value of the banked stack pointer
register `SP_main`. The other stack pointer banked register, `SP_process`, is in
an unknown state except for being 4 byte aligned (bits 1 and 0 are both `0`).
The second address at `0x00000004` is the initial value of the program counter,
`PC`. Bit 0 of this value sets the Thumb mode of the processor (bit 24 in the
`EPSR`) and must be 1. Since this is a ARMv7E-M specification processer it only
has Thumb mode.

Execution starts in the [cores](https://github.com/PaulStoffregen/cores/)
repository with the program counter at `0x01BC`. This address is the beginning
of the `ResetHandler` in the `teensy3/mk20dx128.c` source file. Up to line `771`
(`while (dest < &_edata) *dest++ = *src++;`) this code sets system registers
which I'll leave as a topic for another day. This line copies the text section
of the executable to data, which is in SRAM.

For this blinking LED code the text section (located in the flash) is 1540 bytes
long and located at the end. For this example it's address range is [`0x31F0`,
`0x37F4`). The corresponding data in SRAM is located at the adddress range
[`0x1FFF8440`, `0x1FFF8A44`). Note that the first valid address of SRAM for
Teensy 3.2 is `0x1FFF8000`. It does this by copying word by word (4 bytes).
After 2351 instructions since starting the system finally falls through the
branch in the while loop. I hope those 1540 bytes are worth it, we'll get to
those later.
