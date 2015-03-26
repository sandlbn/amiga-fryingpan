# Building FryingPan #

I'm pretty sure you will want to build your copy of the software. This short document will explain how to do that.

## Before You Begin ##

  1. You must be sure you have installed a copy of ZeroHero's cross toolchain. You can download a copy of the toolchain [here](http://zerohero.se/). It may be also a good idea to build your own toolchain for AROS, as the sources compile for this platform, too. This chapter nor web site do not cover the details on how to do that.
  1. The hierarchy of the source directories is also important. While it is **advised** that you **clone the directories to separate working sets** (it's best that you do not mix them, nor put one under another), you will still have to make a symbolic link, so that individual components can access the framework. You can do this using linux _ln_ command, like in example:
```
cd FryingPan
ln -s ../Generic .
```
  1. You will need to apply certain modifications to **Makefile.mk** file; initially your modifications may be limited to:
    * **TARGETS** - specifies what build targets you are interested in (defaults to: _os3 os4 aros morphos_)
    * **WSPATH** - must point to your workspace path, that is, the place where you hold the code (defaults to _$(HOME)/workspace_)
    * **BINPATH** - must be a valid path, where make will look for toolchain binaries (defaults to _/usr/local/amiga/bin_)
  1. At some point you may be also interested to substitute the tools used to compile the sources. in this case, look up the **Makefile.mk** file and replace appropriate variables; typically these should be set to:
```
GENLIB		= $(WSPATH)/Generic/lib
GENINC		+= $(INCOPTS) -I$(WSPATH)/Generic -I$(WSPATH)

M68K_CC 	= $(BINPATH)/m68k-amigaos-gcc 
AROS_CC		= $(BINPATH)/i686-aros-g++
MORPHOS_CC 	= $(BINPATH)/ppc-morphos-gcc
OS4_CC		= $(BINPATH)/ppc-amigaos-g++
LINUX_CC	= /usr/bin/g++

M68K_AR 	= $(BINPATH)/m68k-amigaos-ar
AROS_AR		= $(BINPATH)/i686-aros-ar
MORPHOS_AR 	= $(BINPATH)/ppc-morphos-ar
OS4_AR		= $(BINPATH)/ppc-amigaos-ar
LINUX_AR	= /usr/bin/ar

M68K_STRIP	= $(BINPATH)/m68k-amigaos-strip
AROS_STRIP	= $(BINPATH)/i686-aros-strip
MORPHOS_STRIP	= $(BINPATH)/ppc-morphos-strip
OS4_STRIP	= $(BINPATH)/ppc-amigaos-strip
LINUX_STRIP	= /usr/bin/strip

AROS_RLIBOPTS	+= -L$(GENLIB)/$(AROSRBIN)
MOS_RLIBOPTS	+= -L$(GENLIB)/$(MORPHOSRBIN)
M68K_RLIBOPTS	+= -L$(GENLIB)/$(M68KRBIN)
OS4_RLIBOPTS	+= -L$(GENLIB)/$(OS4RBIN)

AROS_DLIBOPTS	+= -L$(GENLIB)/$(AROSDBIN)
MOS_DLIBOPTS	+= -L$(GENLIB)/$(MORPHOSDBIN)
M68K_DLIBOPTS	+= -L$(GENLIB)/$(M68KDBIN)
OS4_DLIBOPTS	+= -L$(GENLIB)/$(OS4DBIN)
```

That should be it, at least in case you're using Linux to build your sources.


## Compiling things ##

Now that you have your workspace prepared, you should be all set to go. Compiling the sources should be relatively simple, yet you need to do it in right order; the projects depend on each other, but not in the way they build each other.

The order of building the sources is:
  1. **Generic/Startup** - startup code for all OSes
  1. **Generic/LibC** - well it shouldn't be called that ;-) very little in common
  1. **Generic/Generic** - Generic framework - multiple classes and pieces
  1. **Generic/GenericLib** - Library framework - all you need to build a library
  1. **Generic/GenericDev** - Device framework - just like above, for devices
  1. **Expat** - Expat library port
  1. **DTLib** - Datatypes (FP specific)
  1. **ISOBuilder** - On-The-Fly ISO generator (FP specific)
  1. **Optical** - low level engine (FP specific)
  1. **FP** - main application

the above list explains also more or less the build dependency: if you make modification in something from the top of the list, you should rebuild pretty much everything that follows.

## Building Packages ##

When you're done, you will most likely want to build your distribution / installation packages. To do that, you are required to follow one last step: enter the **FPData** directory and launch a script you will find there: **accumulate.sh**. This script will copy all the files that you've built and put it in a nice _lha_ archive.
```
cd FPData
./accumulate.sh
```

At this point you should be done, looking at your shiny new lha packages.