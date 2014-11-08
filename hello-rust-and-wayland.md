title: Hello Rust and Wayland

I decided to get my feet a bit wet with Rust and Wayland, at the same time. That
decision may prove to be foolish, but whatever. I wrote some simple Rust
bindings for Wayland located on [GitHub][1]. Right now it links against
`libwayland-client.so` and contains all of the functions from `wayland-client.h`
and `wayland-client-protocol.h`. All of the functions are exposed from the `raw`
module and do work.

My next goal is to write better Rust style bindings while learning more about
the Wayland protocol. It seems, at least with Weston, clients can cause the
compositor to segfault quite easily. For instance, I was testing it using Weston
from within X and didn't respond to wl_shell_surface ping events. Whenever I
switched workspaces from and back to Weston, it would crash.

I wrote an example program that, for now, just creates a transparent black box
on the screen and that's it. This is just to demostrate the bindings and the
protocol. The example is also on my [GitHub][2]. To get this example I followed
this [hello_wayland][3] tutorial. I believe the Rust `main` function makes it a
bit clearer as to what the actual protocol is than the C example. Hopefully the
bindings will improve. As always, I'm open to suggestions.

[1]: https://github.com/eyolfson/rust-wayland
[2]: https://github.com/eyolfson/rust-wayland-examples
[3]: http://hdante.wordpress.com/2014/07/08/the-hello-wayland-tutorial/
