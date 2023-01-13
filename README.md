# GC/Wii Splitting and You: A Primer

## Preface

If the game you are decompiling has a symbol map or an .elf file, or if a very similar game code wise has one of the two, you probably don't need to use this guide. In that case, I would recommend using mapdas to make use of the symbol map if it has one, and any elf disassembling tool if the game has an .elf file.

## Introduction

When beginning a decompilation of any kind, one of the things that people bemoan working on the most but nonetheless is the most important thing to do before anything is splitting. Without splitting the massive code file that you start out with, labeling functions, decompiling files, and reverse engineering all become much harder. On top of the file being hard to edit and navigate through, not having established file boundaries makes it way hard to not only label and split data sections correctly, but also to actually decompile files, which depend on all of the other things being done properly.

For GC/Wii specifically, this entails splitting up the init and text sections (which contain the game's code) into separate files, and splitting up the different data sections (rodata, data, bss, etc...) into each of those files based on where they get used (there are some exceptions to this which I will address later)

This guide will outline the general steps you want to follow when splitting a disassembly into separate files.


## Getting Started

First and foremost, if using a tool that doesn't automatically put each section into a separate file (doldisasm.py for example), you want to do this yourself. This will make things better organized and easier to manage.

## Splitting Process

These are the general steps you want to follow when splitting the disassembled code/data for a decompilation.

### 1) Identifying core libraries

The first thing you want to do is to identify where the two core libraries that each GC/Wii game have are located (GC/Wii SDK and MSL). You can do this by cross referencing with the assembly code from another decompilation, starting from the first file in both libraries and moving down until you find a matching file. From there, you want to identify where the library code starts and ends by moving up/down through all the files the libraries can include until you reach both ends, optionally splitting the library code into its own file for now. You can (and should) also make use of different strings included in the dol to help identify files/where each library starts.

### 2) Identifying other common libraries

After finding the MSL and GC/Wii SDK code, you want to figure out the common/ libraries/middleware the game uses (such as CriWare). There are several ways/methods you can use in order to find out what libraries it uses/might use:

- Strings inside the executable
- File formats used by the game
- Box art (usually at the bottom of the back cover with all the copyright info)

Once you find out what other libraries it uses (including in-house libraries such as EGG or JSystem) you want to look for games with symbols that also use those libraries, or existing decompilation projects that also use those libraries. For example, if you are decompiling any Gamecube game developed by Nintendo, it very likely uses JSystem, so you would be able to reference several preexisting decompilation projects to split/label the code.

Same as above, you want to find where each library starts and ends, and split each into their own file.

### 3) Splitting/labeling a file

Once you've separated the code into the different libraries you've identified in the code, the next step is to split them into all the separate files the code was originally in.

After splitting each file, you then want to split the data sections according to each file you split. If you want, you can do this after splitting all the code.

### 4) Splitting data

To be written.


## Things to make use of/look out for when splitting

### Sinit functions
NOTE: (if your game does not use C++, you can ignore this)

Each file can only have 1 sinit function, or the static initialization function. These functions are listed out in the ctors section, which has a list of these functions so that all the static data in each file can be initialized at startup. (this is handled by the MSL library). The sinit function is usually at the end, except for it if uses any functions below it, or if the file has instances of weak functions (such as destructors) that were kept as the global instance of that function. To start with though, you can just treat the sinit and any functions it uses as file boundaries, splitting the code at those points. However, it's important to note that not all files/translation units will have an sinit function.

### Floats
In most games, all float constants are put into the `.sdata2` section. In this case, the compiler only keeps one copy of each float constant for each file, removing any duplicate values. **However**, this only happens on a file per file basis, meaning that each copy of any one float constant approximately marks a file boundary.

### RTTI
NOTE: (if your game does not use C++, you can ignore this)

To be written.

### String Pooling
To be written.
