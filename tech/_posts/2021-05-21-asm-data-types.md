---
title: data types in assembly
subtitle: all these words, and still there is nothing to hear
slug: data types assembly
tags: asm notes
---

Defining variables in strictly typed programming languages looks something like this: `datatype varName = varValue`. However, after compilation, data type information is "hidden" as data types are turned into [words](https://en.wikipedia.org/wiki/Word_\(computer_architecture\)). I say hidden because at first glance it's not always obvious what data type a value used to be unless the CPU is executing an instruction that is intended for specific data types (e.g. `mov` vs. `movss` in x64).

Depending on the processor, word length/width/size can vary. In x64 systems, a single word is 2 bytes. This means that any time you see `DWORD` (double word) or `QWORD` (quadruple word) in an x64 binary you would simply multiply the standard word size by 2 or 4 to get the size of the data. However, on ARM systems, a word is 4 bytes in size, so a `DWORD` in ARM is 8 bytes and so on.

The best way to identify a piece of hardware's word size would be to simply consult the hardware manufacturer's manual or by using your favorite search engine. You can usually acquire these online via official channels or maybe unofficial channels if the manufacturer is now defunct. If you cannot find a hardware manual, you could possibly watch how much memory is used for a word/dword/qword to identify the standard word size.

That said, identifying the correct data type may or may not be relevant depending on your or the code's goals. That is, identifying variable as a `float` vs. an `int` may not be relevant if the code never uses the fractional part of the number--some developer may have just coded the value as a `float` for no real reason. However, misinterpreting an `int` value as a `char` or vice versa can cause confusion which will lead to misunderstanding a particular function.