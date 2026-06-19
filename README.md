<style>
p{margin-left:.5cm;max-width:21cm}
li{margin-left:.5cm;max-width:18cm}
li p{margin-left:0}
</style>
# abcm2ps

> This is a maintained edition of Jean-François Moine's abcm2ps, with fixes and
> fine-tuning for Great Highland Bagpipe score rendering. abcm2ps has always
> supported bagpipe music, but bugs in that area tend to go unnoticed upstream
> as bagpipe music is understandably not Moine's primary focus. The original GitHub repository was archived
> in 2024; Moine's work continues on his [Fossil repository](https://chiselapp.com/user/moinejf/repository/abcm2ps).
> Full credit for the original program belongs to him.

### Overview

abcm2ps is a C program which converts music tunes from the ABC music notation
to PostScript or SVG.

Based on [abc2ps](https://github.com/methf/abc2ps),
the Postscript generator for ABC music notation by Michael Methfessel,
abcm2ps was first developped to print barock organ scores that have independant
voices played on one or many keyboards and a pedal-board
(the 'm' of abcm2ps stands for many or multi staves/voices).
Since this time, it has evolved so it can render many more music types.

Note that this program is at end of life. Its successor is
[abc2svg](https://chiselapp.com/user/moinejf/repository/abc2svg).

### Features

The features of abcm2ps are based on the
[ABC draft 2.2 (February 2013)](http://abcnotation.com/wiki/abc:standard:v2.2).
The differences are listed in the
[abcm2ps/abc2svg documentation](http://moinejf.free.fr/abcm2ps-doc/features.html).

### Installation and usage

The installation procedure is described in the file INSTALL.
To build the program with default settings run

```
    ./configure
    make
```

Basically, the program usage is:

    abcm2ps [options] file1 [file1_options] file2 [file2_options] ...

where file1, file2, .. are the ABC input files.
This generates a Postscript file (default name: `Out.ps`).

Run `abcm2ps -h` to know the list of the command line options.

### Documentation

- The file abcm2ps.rst describes all command-line options.

  When `abcm2ps` is installed, this file may be displayed by `man abcm2ps`.

- The features and format parameters are described in
    [abcm2ps-doc](http://moinejf.free.fr/abcm2ps-doc/index.html).

### This edition — Great Highland Bagpipe focus

This repository began as a fork of the archived GitHub mirror of abcm2ps and has since
been established as an independent project. The full git history from the original
repository is preserved, and upstream changes from Moine's Fossil repository through
v8.14.18 (January 2026) have been merged.

The focus going forward is catching and correcting rendering issues specific to Great
Highland Bagpipe scores — gracenotes, embellishments, and PostScript output details
that are easy to miss if you are not actively producing pipe music. Changes of this
kind are unlikely to find their way upstream, as Moine's active development has moved
to [abc2svg](https://chiselapp.com/user/moinejf/repository/abc2svg).

Some historical context: in the early 2000s, bagpipe-related PostScript suggestions
were contributed to moinejf directly and incorporated into the original abcm2ps. This
project continues that tradition of refining the program for pipe music, now as a
standalone maintained edition.

### Links

Author's page: [http://moinejf.free.fr/](http://moinejf.free.fr/)

To know more about the ABC music notation, have a look at
    [abcnotation](https://abcnotation.com/).

Guido Gonzato maintains many abcm2ps binaries and more documentation at
    [abcplus](https://abcplus.sourceforge.net/).
