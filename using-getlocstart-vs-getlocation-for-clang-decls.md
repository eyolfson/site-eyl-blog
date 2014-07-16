title: Using getLocStart vs. getLocation for Clang Decls

Functions show some minor variation (that you would expect). I'll demonstrate
the difference in two small snippets of code. Consider the following template
class located in the header file `foo.h`:

    #pragma once

    template <typename T> class foo {
    public:
       bool bar(int)
       {
          return true;
       }
       bool bar(T t);
    };

    template<typename T>
    bool foo<T>::bar(T t)
    {
       return false;
    }

Next, consider the following code that uses `foo.h`:

    #include "foo.h"

    template class foo<float>; // template instantiation

    int main()
    {
        foo<float> f;
        return f.bar(1.0f);
    }

Ignoring the template instantiation for now, with `getLocStart`, Clang reports
two methods. One at `foo.h:5:5` and another at `foo.h:9:5`. Note, both begin at
`bool`. With `getLocation`, Clang reports two methods again with different
offsets. One at `foo.h:5:10` and another at `foo.h:9:10`. Note, both begin at
`bar`. You'll likely want to use `getLocation` instead of `getLocStart` solely
for this reason.

Template instantiation shows a lot of variation (that I didn't expect) between
`getLocStart` and `getLocation` for `Decl`s. Clang creates two `CXXRecord`s,
one for the template and another for the template instantiation. The previous
code locations are for the methods in the regular template `CXXRecord`. For the
template instantiation `CXXRecord`, if you use `getLocStart`, Clang reports the
location of the second `bar` at `foo.h:13:1`. This location isn't what I
expected. I expected the two `CXXRecord`s to have the same method `Decl`
locations since the declarations didn't move. If you use `getLocation` instead
Clang reports the second `bar` at `foo.h:9:10` for both `CXXRecord`s. This is
what I expected and why I use `getLocation` instead of `getLocStart`.
