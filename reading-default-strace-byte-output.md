title: Reading Default strace Byte Output

While exploring the Wayland protocol I fired up `strace` in order to read the
raw bytes sent over the protocol, and I got surprised. I assumed if an argument
is `void *` it would show the bytes of the argument. Nope, it shows it as a
string with octal numbers to represent unknown numbers. This means 15 is
represented as `\17`.  I'm sure there's some historical reason for octal
numbers, but come on.

If you want non-ASCII strings (since apparently `void *` is a string) to print
in a hexadecimal format use `strace -x`. Better yet, if you're just looking at
bytes, to print all strings in hexadecimal string format use `strace -xx`.
