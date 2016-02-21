# Copyright and Licensing #
See LICENSE in the distribution. This is covered under a BSD license.

(Note, license applied on all work since 0.3 which was originally released into the public domain)

# Introduction #

Unsafe integer operations are a major cause of software defects even in modern
day software.  C is the underlying language for most high level languages
(Ruby, Python, Java, etc.) in addition to being in widespread general use.
C is a preferred language for high performance programming and is
often used for media file parsing and manipulation.

Integer overflows occur when the calculated integer requires more storage from
the computing platform than is available.  If a number is too large, not all of
its information can be stored.  This has dangerous side effects. For a detailed
and thorough discussion on integer overflows, please check out CERT's website
on Secure Coding(1) and even Wikipedia(2).

(1) https://www.securecoding.cert.org/confluence/display/seccode/CERT+C+Secure+Coding+Standard
(2) http://en.wikipedia.org/wiki/Integer_overflow


# Requirements #

safe\_iop was designed explicitly with GNU GCC in mind and has only been tested
with it.  Your mileage may vary.  Please let me know if it works for you with
different compilers or on different platforms, and I'll update the Compatibility
section below!

In addition, your system must supply limits.h and assert.h for safe\_iop to
function as expected.  It is possible to remove the dependence on both, but it
breaks general portability.

# Usage #

safe\_iop comes in two pieces, safe\_iop.h and safe\_iop.c.  safe\_iop.h provides
extensive macros for performing safe integer operations quickly and easily.
safe\_iop.c contains some testing code to make sure the package works on your
system and a preliminary format string interface, safe\_iopf.  safe\_iopf is not
for the faint of heart as it is currently under development.  The remainder of
this document will focus on safe\_iop.h.

In order to use safe\_iop, you will need to place safe\_iop.h in your compiler's
include path either by copying it somewhere like /usr/include, using an include
flag -I/opt/safe\_iop/include, or whatever other way you prefer. You will then
need to include the header in the source file you will use the functions from.
E.g., #include <safe\_iop.h>

safe\_iop provides macros which check the validity of a given integer operation.
It supports the following operations:
  * multiplication: safe\_mul()
  * division:       safe\_div()
  * addition:       safe\_add()
  * subtraction:    safe\_sub()
  * left shift:     safe\_shl() (trunk [r21](https://code.google.com/p/safe-iop/source/detail?r=21)+)
  * right shift:    safe\_shr() (trunk [r21](https://code.google.com/p/safe-iop/source/detail?r=21)+)

All of these macros take a result pointer, or NULL, as the first argument.  The
subsequent argument should be the two values to operate on.  They then return
true or false depending on if the operation is safe or not. (If NULL is given,
a true or false value will be returned.)
```
  uint32_t a = 100, b = 200, c = 0;
  if (safe_mul(&c, a, b)) printf("c is %u\n", c);
```

In addition, there are versions of these functions for multiple sequential operations:
```
  uint32_t a = 100, b = 200, c = 300, d = 0;
  if (safe_mul3(&d, a, b, c)) printf("d is %u\n", d);
```

safe

&lt;op&gt;

3-5() are all available.

There are also two helper functions for safe increment/decrement∴
```
  int a = 0;
  ...
  if (!safe_inc(&a)) abort();
  ...
  if (!safe_dec(&a)) abort();
```
(Available in trunk after change [r19](https://code.google.com/p/safe-iop/source/detail?r=19))

It is important to note that it is safest to pass integer of similar types to
safe\_iop.  However, as of change [r20](https://code.google.com/p/safe-iop/source/detail?r=20), safe\_iop supports automatic type casting to the type of the first operand.  However, if this cast cannot be performed without losing data, safe\_iop will fail.  The failure mode depends on the compilation options.  If -DNDEBUG is defined, the safe\_iop will return false.  If it is not defined, assert() is called and the process will terminated.
For example,
```
  uint32_t a = 100, c = 0;
  uint8_t b = 20;
  if (safe_add(&c, a, b)) /* b is cast up to a uint32_t safely! */
```


Examples can be found in the examples/ directory.

## safe\_iopf ##

If you'd like to use the format string function, do so at your own peril :-)
If you like it and would like to send me a patch to make it awesome, I'd
appreciate it!  To use, just include the c file in your build, or build the
shared library and link it to your app:
```
  make so # or make dylib for OS X 
  ...
  gcc yourapp.c ... -lsafe_iop
```

More to come!

# Compatibility #

See http://code.google.com/p/safe-iop/wiki/Compatibility


# Credit where credit is due #

  * The functions used in this library were largely drawn from the examples provided in CERT's secure coding standard.
  * Thanks to peter@valchev.net for reviews, comments, enthusiasm, and multiple platform tests!
  * Thanks to taviso@sdf.lonestar.org for the pointing out stupid API decisions and cross-checking my logic.

# Changes #

The changes and todo list can be found in include/safe\_iop.h

# Contributions, corrections, suggestions, flames . . . #

Please drop me an email if I'm doing something completely stupid, you love
using the library, you have a patch or recommendation, or for whatever other
reason.  I hope this software helps out a bit!