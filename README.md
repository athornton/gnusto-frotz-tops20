# Gnusto-Frotz-Tops20

## Status

It seems to play Zork 1.  Large z3 games, and other z-code games, run
out of memory.

It passes the tests in `czech` but not all the ones in `praxix`.

I know why, in a general sense:

```
@type testc.c
#include <stdio.h>
int main ( int argc, char **argv) {
    char c=-1;
    short s=-1;
    printf("Max unsigned char is %d\n", (unsigned char) c);
    printf("Max unsigned short is %d\n", (unsigned short) s);
}
@cc -o testc testc.c
KCC:   TESTC
 "testc.c", line 8: [Note] Parameter "argc" not used
       (main+6, p.1 l.7): "Max unsigned short is %d\n", (unsigned short) s); }

 "testc.c", line 8: [Note] Parameter "argv" not used
       (main+6, p.1 l.7): "Max unsigned short is %d\n", (unsigned short) s); }

<ADAM>TESTC.PRE.4
<ADAM>TESTC.FAI.4
FAIL:  TESTC
LINK:  Loading
@testc
Max unsigned char is 511
Max unsigned short is 262143
```

And this line in the Dumb-Frotz README, in the CAVEATS:

```
     - lack of 8-bit char and 16-bit short.  I didn't bother to think
       much about this.  If you're using a 36-bit Honeywell or
       something, let me know.
```

## Introduction

### What?

This program has only one function: create a version of dumb-frotz that
is playable on TOPS-20 on a PDP-10.

It does this by acquiring dumb-frotz 2.32r1, transmogrifying it so that
the TOPS-20 linker can handle the symbols, and outputting the mogrified
sources.

This isn't as easy as it sounds, because:

[https://github.com/PDP-10/panda/blob/master/files/kcc-6/kcc/user.doc#L519]

```
However, the situation is different for symbols with external
linkage, which must be exported to the PDP-10 linker.  Such names are
truncated to 6 characters and case is no longer significant.
```
So you need to map all the symbols in the header files, everything declared
"extern" in the source files, and also make sure that your input file names
are unambiguous and no more than six characters before the extension.

But even that's not enough, because there are a number of places where
Frotz is assuming 8-bit chars and 16-bit shorts.  So now
`gnusto-frotz-tops20` pulls from my fork of the sources at
https://github.com/athornton/tops20-frotz, where I've been hacking to
try to sanitize the code such that TOPS-20 is happy with it.

### Why?

To bring Zork back home.

More generally, with the dump of "The Infocom Drive" showing up on the
Internet, I decided it'd be fun to be able to play Z-machine files on a
PDP-10, as God and Nature intended.

### License

Gnusto-Frotz-Tops20 is MIT licensed.

Copyright 2019 Adam Thornton <athornton@gmail.com>

## Usage

You will need a Perl 5 interpreter with the standard Perl libraries, a
working `sed`, and a working `git`.  You will need to be in a writeable
directory.

### Generating compilable source

In there, just execute `gnusto-frotz-tops20`.  This will generate a
bunch of files in the `output` directory.

### Compiling that source

Copy those files to a TOPS-20 system (e.g. FTP in text mode) and compile
them with `cc -o frotz *.c`.  That will generate `frotz.exe`, which is
the TOPS-20 executable.

I'm using
[the late Mark Crispin's "Panda" distribution of TOPS-20](http://panda.trailing-edge.com/)
and the [klh10](https://github.com/DavidGriffith/klh10) interpreter.

### Running a game

`frotz <directory>game` seems to work OK for Zork 1.  It's running out
of memory on other games but that may be my emulator configuration.  A
couple of array load/store instructions aren't working right yet, and
the memory stream stuff doesn't work, but that doesn't matter for the
classic games.

