### Idea scratched

## C++ named arguments proposal

Basic problem
===========

Having a function

    ssize_t write_datagram(char const* data, size_t size, bool is_reliable);

How do we understand what a boolean parameter means at the call site without referring to the function definition?

    write_datagram(data, size, true); // what does true mean?

Usual approach
============

    enum class datagram_type
    {
        non_reliable = 0,
        reliable = 1
    };

    ssize_t write_datagram(const char* data, size_t size, stream::datagram_type is_reliable);

Now we can easier see what the argument is about:

    write_datagram(data, size, datagram_type::reliable);

Problem: need to create a (scoped) enum where a simple bool would suffice.

Named argument approach
=====================

    ssize_t write_datagram(const char* data, size_t size, bool is_reliable:);

and a call looks like:

    write_datagram(buf, size, is_reliable: true);

Easier to read, no additional enums. The approach also helpful for various graphic libraries where a lot of numerical arguments is used but it's close to impossible to understand their order without reading the docs.

    rect(100, 200, 300, 500); // width? x coordinate? which is which?

    rect(left: 100, top: 200, width: 300, height: 500); // clear!

Of course, there are other ways to achieve this, like fluent interfaces etc. This method seems pretty elegant to me.

More details
==========

Named arguments must always be named, so the cases like this work:

    class rect {
        rect(int left:, int top:, int right:, int bottom:);
        // Named arguments may have default values as well
        rect(int left: = 0, int top: = 0, int width: = 0, int height: = 0);
    };

    rect r = rect(width: 200, height: 100);
    rect r2 = rect(left: 0, right: 200, top: 0, bottom: 100);

Compiler would have to resolve the correct method signature using argument names.

Ambiguity may arise if there are two different signatures with default values
and the call site make it impossible to distinguish one signature from another.
Such programs are malformed.

The order in which named arguments are pushed on stack is implementation
dependent, but most obvious method is to allocate them in order of declaration.


[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/berkus/cpp_named_args/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

