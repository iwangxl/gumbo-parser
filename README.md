Gumbo - A pure-C HTML5 parser.
============

Gumbo is an implementation of the [HTML5 parsing algorithm][] implemented
as a pure C99 library with no outside dependencies.  It's designed to serve
as a building block for other tools and libraries such as linters,
validators, templating languages, and refactoring and analysis tools.

Goals & features:

* Fully conformant with the [HTML5 spec][].
* Robust and resilient to bad input.
* Simple API that can be easily wrapped by other languages.
* Support for source locations and pointers back to the original text.
* Relatively lightweight, with no outside dependencies.
* Passes all [html5lib-0.95 tests][].
* Tested on over 2.5 billion pages from Google's index.

Non-goals:

* Execution speed.  Gumbo gains some of this by virtue of being written in
  C, but it is not an important consideration for the intended use-case, and
  was not a major design factor.
* Support for encodings other than UTF-8.  For the most part, client code
  can convert the input stream to UTF-8 text using another library before
  processing.
* C89 support.  Most major compilers support C99 by now; the major exception
  (Microsoft Visual Studio) should be able to compile this in C++ mode with
  relatively few changes.  (Bug reports welcome.)

Wishlist (aka "We couldn't get these into the original release, but are
hoping to add them soon"):

* Support for recent HTML5 spec changes to support the template tag.
* Support for fragment parsing.
* Full-featured error reporting.
* Bindings in other languages.

Installation
============

To build and install the library, issue the standard UNIX incantation from
the root of the distribution:

    $ ./autogen.sh
    $ ./configure
    $ make
    $ sudo make install

Gumbo comes with full pkg-config support, so you can use the pkg-config to
print the flags needed to link your program against it:

    $ pkg-config --cflags gumbo         # print compiler flags
    $ pkg-config --libs gumbo           # print linker flags
    $ pkg-config --cflags --libs gumbo  # print both

For example:

    $ gcc my_program.c `pkg-config --cflags --libs gumbo`

See the pkg-config man page for more info.

There are a number of sample programs in the examples/ directory.  They're
built automatically by 'make', but can also be made individually with
`make <programname>` (eg. `make clean_text`).

To run the unit tests, you'll need to have [googletest][] downloaded and
unzipped.  The googletest maintainers recommend against using
`make install`; instead, symlink the root googletest directory to 'gtest'
inside gumbo's root directory, and then `make check`:

    $ unzip gtest-1.6.0.zip
    $ cd gumbo-*
    $ ln -s ../gtest-1.6.0 gtest
    $ make check

Gumbo's `make check` has code to automatically configure & build gtest and
then link in the library.

Basic Usage
===========

Within your program, you need to include "gumbo.h" and then issue a call to
`gumbo_parse`:

```C++
#include "gumbo.h"

int main(int argc, char** argv) {
  GumboOutput* output = gumbo_parse(argv[1]);
  // Do stuff with output->root
  gumbo_destroy_output(&kGumboDefaultOptions, output);
}
```

See the API documentation and sample programs for more details.

A note on API/ABI compatibility
===============================

We'll make a best effort to preserve API compatibility between releases.
The initial release is a 0.9 (beta) release to solicit comments from early
adopters, but if no major problems are found with the API, a 1.0 release
will follow shortly, and the API of that should be considered stable.  If
changes are necessary, we follow [semantic versioning][].

We make no such guarantees about the ABI, and it's very likely that
subsequent versions may require a recompile of client code.  For this
reason, we recommend NOT using Gumbo data structures throughout a program,
and instead limiting them to a translation layer that picks out whatever
data is needed from the parse tree and then converts that to persistent
data structures more appropriate for the application.  The API is
structured to encourage this use, with a single delete function for the
whole parse tree, and is not designed with mutation in mind.

Python usage
============

To install the python bindings, make sure that the
C library is installed first, and then `sudo python setup.py install` from
the root of the distro.  This installs a 'gumbo' module; `pydoc gumbo`
should tell you about it.

Recommended best-practice for Python usage is to use one of the adapters to
an existing API (personally, I prefer BeautifulSoup) and write your program
in terms of those.  The raw CTypes bindings should be considered building
blocks for higher-level libraries and rarely referenced directly.

[HTML5 parsing algorithm]: http://www.whatwg.org/specs/web-apps/current-work/multipage/#auto-toc-12
[HTML5 spec]: http://www.whatwg.org/specs/web-apps/current-work/multipage/
[html5lib-0.95 tests]: https://github.com/html5lib/html5lib-tests
[googletest]: https://code.google.com/p/googletest/
[semantic versioning]: http://semver.org/
