The other day I was doing some work on the DMD compiler. I ran into an issue
where my new code was compiling perfectly fine when using the latest release of
DMD as the host compiler but failed when using the bootstrap compiler.

Since the DMD compiler is written in D itself, it needs a way to bootstrap
itself. The way this is done (or one of the alternatives) is that the last
version of DMD which was written in C++, is still available and maintained. That
version is DMD 2.076.0. The C++ version of DMD can be compiled using a C++
compiler, to get a D compiler. This newly built D compiler can then be used as
the host compiler when compiling the latest version of DMD.

So I thought: "No problem, I use my trustworthy [DVM](https://github.com/jacob-carlborg/dvm)
to just install DMD 2.076.0". First install the compiler:

```
$ dvm install 2.076.0
```

Then activate it:

```
$ dvm use 2.076.0
```

Then make sure the right version is active:

```
$ dmd --version
dyld: lazy symbol binding failed: Symbol not found: _dyld_enumerate_tlv_storage
  Referenced from: ~/.dvm/compilers/dmd-2.076.0/osx/bin/dmd
  Expected in: /usr/lib/libSystem.B.dylib

dyld: Symbol not found: _dyld_enumerate_tlv_storage
  Referenced from: ~/.dvm/compilers/dmd-2.076.0/osx/bin/dmd
  Expected in: /usr/lib/libSystem.B.dylib

Abort trap: 6
```

"Hmm, right, I forgot about that problem". I'm running this on macOS Catalina
(10.15). Ever since DMD got support for native thread local storage on macOS, the
runtime has relied on a function in the dynamic linker called `dyld_enumerate_tlv_storage`.
This is private function, meaning it's not part of the public API. That means
Apple doesn't provide any form of guarantees about this symbol. It may change or
be removed at any new release. This symbol has existed since macOS Lion (10.7),
but in macOS Catalina it was removed. This caused all D application built using
DMD or LDC to not work on macOS Catalina or later. This was fixed in the D
runtime shipped with DMD 2.088.0. But problem is that all applications need to
be recompiled, to remove the dependency on this symbol and make them work again.

I'm thinking: "No problem, I'll just compile DMD 2.076.0 myself using the latest
version of DMD". I checked out the `2.076.0` tag of DMD and started compiling
using the latest version of DMD. Quite soon the build process stopped with a
bunch of compile errors. "Ok, let's try an older version". Same problem with the
previous version of DMD. I tried to compile using all version between
2.088.0 and 2.094.0 but all of them had the same problem. 2.088.0 is the oldest
version of DMD with the fix for the above missing symbol.

After that I start to fix the compile errors. After fixing all errors and running
the compiler again, new errors appeared.
