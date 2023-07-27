---
title: data types in assembly
subtitle: all these words, and still there is nothing to hear
tags: asm notes
ref: ["scar symmetry/pitch black progress/oscillation point","https://www.youtube.com/watch?v=d7cBwkz8tcE"]
---

Defining variables in strictly typed programming languages looks something like this: `datatype varName = varValue`. However, after compilation, data type information is "hidden" as data types are turned into [words](https://en.wikipedia.org/wiki/Word_\(computer_architecture\)). I say hidden because at first glance it's not always obvious what data type a value used to be unless the CPU is executing an instruction that is intended for specific data types (e.g. `mov` vs. `movss` in x64).

Depending on the processor, word length/width/size can vary. In x64 systems, a single word is 2 bytes. This means that any time you see `DWORD` (double word) or `QWORD` (quadruple word) in an x64 binary you would simply multiply the standard word size by 2 or 4 to get the size of the data. However, on ARM systems, a word is 4 bytes in size, so a `DWORD` in ARM is 8 bytes and so on.

The best way to identify a piece of hardware's word size would be to simply consult the hardware manufacturer's manual or by using your favorite search engine. You can usually acquire these online via official channels or maybe unofficial channels if the manufacturer is now defunct. If you cannot find a hardware manual, you could possibly watch how much memory is used for a word/dword/qword to identify the standard word size.

{% highlight c linenos %}
int main(void)
{
    int x = 97;
    float y = 1.2345;
    char z = 'a';
    char s[] = "abcd";
    return 0;
}
{% endhighlight %}

{% highlight plaintext linenos %}
;godbolt.org gcc 11.1
main:
        push    rbp
        mov     rbp, rsp
        mov     DWORD PTR [rbp-4], 0x61 ;int x = 97
        movss   xmm0, DWORD PTR .LC0[rip] 
        movss   DWORD PTR [rbp-8], xmm0 ;float y = 1.2345
        mov     BYTE PTR [rbp-9], 0x61 ;char z = 'a'
        mov     eax, 0
        mov     DWORD PTR [rbp-14], 0x61626364 ;char s[] = "abcd"
        mov     BYTE PTR [rbp-10], 0 ;null byte to s[]    
        pop     rbp
        ret
.LC0:
        .long   0x3f9e0419
{% endhighlight %}

Looking at the code above, we see how different variable type assignments translate into assembly. Where would you begin in attempting to figure out what data type each variable is? Generally, you'd have to use the size of the variable along with context clues including how the variable is used. The short program I wrote doesn't really have a ton of clues, but we can glean some information from it.

The easiest one to point out would probably be the `float` assignment because of the `movss` instruction which is reserved for floating point numbers. The `int` and `char` assignments also makes sense even though it's the same value as the `char` assignment--`int`s are generally 4 bytes long by default whereas `char`s are only 1 byte.

The only non-obvious one would likely be the string. We see a value of 0x61626364 at line 10 that's a `dword` in size. If you didn't know what the ASCII character range was, you may incorrectly assume this value to be an `int` because of the `dword` size and the number that's being used. However, the next line down shows a null byte being added to the end of the value of 0x61626364 so you end up with 0x6162636400. C strings always have an implicit null byte at the end to denote the end of a string, so we can assume the value at `[rbp-14]` is a string because of the addition of this null byte. Without the null byte, it would likely be a coincidence that the value here just happened to be a value that's within the ASCII range.

Identifying the correct data type may or may not be relevant depending on your or the code's goals. That is, identifying variable as a `float` vs. an `int` may not be relevant if the code never uses the fractional part of the number--someone may have just coded the value as a `float` for no real reason other than to increase complexity. However, misinterpreting an `int` value as a `char`/string or vice versa can cause confusion which will lead to misunderstanding a particular function. Therefore, it's important to take all context clues into consideration before settling on a variable's data type.
