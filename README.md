# Gnusto-Frotz-Tops20

## Status

It's not playing games yet, although the executable starts up.

I know why:

```
@type testc.c
#include <stdio.h>
int main ( int argc, char **argv) {
    char c=-1;
    printf("C is %d\n", (unsigned char) c);
}
@cc -o testc testc.c
KCC:    TESTC
 "testc.c", line 5: [Warning] File does not end with EOL (\n)
       (main+3, p.1 l.5):  c=-1;     printf("C is %d\n", (unsigned char) c); }

 "testc.c", line 5: [Note] Parameter "argc" not used
       (main+3, p.1 l.5):  c=-1;     printf("C is %d\n", (unsigned char) c); }

 "testc.c", line 5: [Note] Parameter "argv" not used
       (main+3, p.1 l.5):  c=-1;     printf("C is %d\n", (unsigned char) c); }

<ADAM>TESTC.PRE.1
<ADAM>TESTC.FAI.1
FAIL:  TESTC
LINK:    Loading
@testc
C is 511
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

Copy those files to a TOPS-20 system and compile them with `cc -o frotz
*.c`.  That will generate `frotz.exe`, which is the TOPS-20 executable.
It's also uploaded [here](frotz.exe), although this version doesn't yet
work.

I'm using
[the late Mark Crispin's "Panda" distribution of TOPS-20](http://panda.trailing-edge.com/)
and the [klh10](https://github.com/DavidGriffith/klh10) interpreter.

### Running a game

Well, the bad news is, it's not working yet.  The `frotz` executable
starts all right, and it will print its help message if invoked with no
arguments.  But if you try to point it at a game...

```
$frotz

FROTZ V2.32 - A00235er for all Infocom games. Complies with standard
1.0 of Graham Nelson's specification. Written by Stefan Jokisch in 1995-7.

DUMB-FROTZ V2.32R1 - port for all platforms.  Somewhat complies with standard
9899 of ISO's specification.  Written by Alembic Petrofsky in 1997-8.

Syntax: frotz [options] story-file [graphics-file]

  -a      watch attribute setting
  -A      watch attribute testing
  -h #    screen height
  -i      ignore runtime errors
  -I #    A00235er number to report to game
  -o      watch object movement
  -O      watch object locating
  -p      alter piracy opcode
  -P      transliterate latin1 to plain ASCII
  -R xxx  do runtime setting \xxx before starting
            (this option can be used multiple times)
  -s #    random number seed value
  -S #    transscript width
  -t      set Tandy bit
  -u #    slots for multiple undo
  -w #    screen width
  -x      expand abbreviations g/x/z

While running, enter "\help" to list the runtime escape sequences.
$sum <.games>zork1.zip
45570    85
$frotz -i <.games>zork1.zip
?Illegal instruction 0 at 0 (PC = 0)
?Undefined operation code
```

And it's not that the game file is corrupt:

```
adam@fileserver:~/src/gnusto-frotz-tops20/output$ ./dfrotz

FROTZ V2.32 - A00235er for all Infocom games. Complies with standard
1.0 of Graham Nelson's specification. Written by Stefan Jokisch in 1995-7.

DUMB-FROTZ V2.32R1 - port for all platforms.  Somewhat complies with standard
9899 of ISO's specification.  Written by Alembic Petrofsky in 1997-8.

Syntax: frotz [options] story-file [graphics-file]

  -a      watch attribute setting
  -A      watch attribute testing
  -h #    screen height
  -i      ignore runtime errors
  -I #    A00235er number to report to game
  -o      watch object movement
  -O      watch object locating
  -p      alter piracy opcode
  -P      transliterate latin1 to plain ASCII
  -R xxx  do runtime setting \xxx before starting
            (this option can be used multiple times)
  -s #    random number seed value
  -S #    transscript width
  -t      set Tandy bit
  -u #    slots for multiple undo
  -w #    screen width
  -x      expand abbreviations g/x/z

While running, enter "\help" to list the runtime escape sequences.
adam@fileserver:~/src/gnusto-frotz-tops20/output$ sum ../games/zork1.zip
45570    85
adam@fileserver:~/src/gnusto-frotz-tops20/output$ ./dfrotz ../games/zork1.zip
 West of House                               Score: 0        Moves: 0

ZORK I: The Great Underground Empire
Infocom interactive fiction - a fantasy story
Copyright (c) 1981, 1982, 1983, 1984, 1985, 1986 Infocom, Inc. All rights
reserved.
ZORK is a registered trademark of Infocom, Inc.
Release 119 / Serial number 880429

West of House
You are standing in an open field west of a white house, with a boarded
front door.
There is a small mailbox here.

>
```

Note the `A00235` there.  Why, yes, "interpret" is more than 6
characters, and why, yes, this is among the perils of machine
translation.
