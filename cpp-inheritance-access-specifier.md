title: C++ Inheritance Access Specifier

Today I forgot exactly how the C++ inheritance access specifier worked, so this
is a note in case I forget again. When declaring a class it's done like
`class Derived : /* HERE */ Base`, where `/* HERE */` is replaced with one of:
`public`, `protected`, or `private`. The rule is pretty simple, the specifier
acts as an upper bound of the access in the derived class. Here's a table of
what that means to make it crystal clear:

Inheritance Access Specifier | Access in Base Class | Access in Derived Class
---------------------------- | -------------------- | -----------------------
public                       | public               | public
public                       | protected            | protected
public                       | private              | private
protected                    | public               | protected
protected                    | protected            | protected
protected                    | private              | private
private                      | public               | private
private                      | protected            | private
private                      | private              | private

As another refresher, here's how you can remember what the access means. The
most restrictive, `private`, means only this class can access the members
(fields or methods). Next, `protected` allows `private` access and allows access
in any derived classes. Finally, `public` allows `private` and `protected`
access and allows access from outside the class.

Here's an example in code:

    class A {
        /* Can access x, y, and z */
    public:
        int x;
    protected:
        int y;
    private:
        int z;
    };
    
    class B : protected A {
        /* Can access x, or y; can't access z */
        /* Note: - changes x from public to protected
                 - only access change is outside class */
    };
    
    class C : private B {
    public:
        /* Can access x, or y; can't access z */
        /* Note: - changes x and y from protected to private
                 - only noticable in derived class */
    };
    
    class D : public C {
        /* Can't access x, y, or z */
        void test() {
            // int a = x;
        }
    };
    
    int main() {
        A a;
        /* Can access a.x; can't access a.y, or a.z */
        B b;
        /* Can't access b.x, b.y, or b.z */
    }
