title: Be Careful of Weak Symbols when Linking

In C++ the `inline` keyword is only hint to the compiler to inline your code,
it may choose to ignore you. Without optimizations it will always generate a
function and never actually inline the call. In order to handle an inline
function defined in different translation units from not breaking the one
definiton per function rule, the compiler exports the inline function as a
*weak symbol*. A weak symbol can have multiple definitions, that are assumed to
all be the same, and the linker will only add one of the definitions to the
produced executable. Take the following source file as an example.

`foo.cpp`:

    inline int f() { return 7; }
    int foo() { return f(); }

If we create an object file out of this with `clang++ foo.cpp -c` and check the
resulting object file with `nm -C foo.o` we get the following output:

    0000000000000000 W f()
    0000000000000000 T foo()

Note that `W` indicates that `f()` is a weak symbol. Now consider another
similar source file that happens to define its own `f()`.

`bar.cpp`:

    inline int f() { return 42; }
    int bar() { return f(); }

These files are in separate translation units so there's no way for the
compiler to warn us that there's multiple definitions of `f()` that are
different since it only sees one per translation unit. The problem is evident
when we combine these two object files into an executable as follows.

`main.cpp`:

    #include <iostream>
    
    int foo();
    int bar();
    
    int main()
    {
        using namespace std;
        cout << "foo = " << foo() << ", bar = " << bar() << '\n';
    }

When we create an executable with `clang++ foo.cpp bar.cpp main.cpp -o main`
the compiler and the linker show no warnings, even though we've broken the
cardinal rule of having multiple definitions for the same function. Recall that
`f()` has a different definition in `foo.cpp` and `bar.cpp`. Because `f()` is a
weak symbol one of the definitions is just dropped silently by the linker. If
we run the program with `./main` we get the output `foo = 7, bar = 7`. This
seems to be because it kept the first definition, `int f() { return 7; }`, and
dropped any other definitions.

If we create the executable with `clang++ bar.cpp foo.cpp main.cpp -o main`
we'll get a different result. In this case `int f() { return 42; }` is defined
first so we'd now expect the linker to use that definition. When we run `./main`
we do get the output `foo = 42, bar = 42`.

Things return to normal if we enable optimizations. This time let's create the
executable with  `clang++ -O1 foo.cpp bar.cpp main.cpp -o main`. In this case
the compiler actually inlines `f()` and it's not present in any object file.
Now we link, there's no collisions, and when we run `./main` we get the expected
output `foo = 7, bar = 42`.

In this case the bug will go away depending on the optimization level you use,
which makes finding the bug more complex. Unfortunately there's no tools that
I'm aware of to help prevent you from shooting yourself in the foot like this.
It's just another one of those things you have to be aware of when you're using
C++.
