title: Use memfd_create for Wayland Shared Memory

I've been playing around with using shared memory for Wayland buffers and didn't
really like the idea of actually having to create a file on the file system
soley to use as shared memory. Although I knew there's ways to deal with
name-clashes, it still felt hacked together to me. Fournately, I found a better
solution: the new `memfd_create` system call in Linux ([commit][1]). This call
never creates a file on the file system and has no name-clashes, everything is
private. This [blog post][2] goes into more detail.

The system call introduced a new feature called "file sealing". With file
sealing you can specify to the kernel that the file backed by anonymous has
certain restrictions such as: no writes occur to the file, and the file size
does not shrink or grow. For Wayland the `F_SEAL_SHRINK` is of interest. This
means the server knows the client will not shrink it's buffer and can read the
file without unexpected errors due to the file shrinking.

[1]: https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=9183df25fe7b194563db3fec6dc3202a5855839c
[2]: https://dvdhrm.wordpress.com/tag/memfd/
