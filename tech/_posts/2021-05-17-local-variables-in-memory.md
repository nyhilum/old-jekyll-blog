---
title: local variables in memory
subtitle: thanks for the memories
slug: local variables in memory
tags: asm
---

In programming, we're taught that variables are stored in memory, but what *exactly* does this mean?

Variables in high level languages are pretty simple to understand. In C, it's simply **int x = 1;**; however, when looking at the compiled code in assembly, it's a bit more complicated.

Note that global variables and local function variables are stored differently. This post only covers local function variables.

Here's a really simple C program:
{% highlight c linenos %}
int main(void)
{
    int x = 13;
    return 0;
}
{% endhighlight %}

Compiling this code on with x64 gcc gives the following output for the **main** function:
{% highlight plaintext linenos %}
; output from godbolt.org
main:
        push    rbp
        mov     rbp, rsp
        mov     DWORD PTR [rbp-4], 13
        mov     eax, 0
        pop     rbp
        ret
{% endhighlight %}

Once the high level code has been converted into assembly, you're not given a helpful looking line like **int x = 13**, but due to the simplicity of the code above, you could probably deduce that the line **mov DWORD PTR [rbp-4], 13** has something to do with the variable assignment in C (and you'd be right).

Let's dissect this line a bit: **mov DWORD PTR [rbp-4], 13**. This line is saying "move the number 13 into a double word size memory location specified by the memory address pointed to by rbp-4". Alternatively, "move 13 to address rbp-4 that's 4 bytes in size". Using a debugger will help visualize this better.

Loading up the binary in gdb, I put a breakpoint right before 13 is moved into the designated memory location.

{% highlight plaintext linenos %}
(gdb) disass main
Dump of assembler code for function main:
   0x0000555555555129 <+0>:     endbr64
   0x000055555555512d <+4>:     push   rbp
   0x000055555555512e <+5>:     mov    rbp,rsp
=> 0x0000555555555131 <+8>:     mov    DWORD PTR [rbp-0x4],0xd
   0x0000555555555138 <+15>:    mov    eax,0x0
   0x000055555555513d <+20>:    pop    rbp
   0x000055555555513e <+21>:    ret
{% endhighlight %}

Examining the memory at **rbp-0x4** at this point will either show junk or a zero value. In this case, it shows 0.

{% highlight plaintext linenos %}
(gdb) x/w $rbp-4
0x7fffffffddec: 0
{% endhighlight %}

After single stepping in the debugger, examining the memory again should yield the value 0xd or 13 in decimal.

{% highlight plaintext linenos %}
(gdb) si
0x0000555555555138 in main ()

(gdb) disass main
Dump of assembler code for function main:
   0x0000555555555129 <+0>:     endbr64
   0x000055555555512d <+4>:     push   rbp
   0x000055555555512e <+5>:     mov    rbp,rsp
   0x0000555555555131 <+8>:     mov    DWORD PTR [rbp-0x4],0xd
=> 0x0000555555555138 <+15>:    mov    eax,0x0
   0x000055555555513d <+20>:    pop    rbp
   0x000055555555513e <+21>:    ret
End of assembler dump.

# note: gdb words are 4 bytes. intel words are 2 bytes
(gdb) x/xw $rbp-4
0x7fffffffddec: 0x0000000d

(gdb) x/dw $rbp-4
0x7fffffffddec: 13

(gdb)
{% endhighlight %}

This is a very trivial example and identifying variables in memory is not always this straightforward but it's a good start. Sometimes the pointer being dereferenced will be another register whose value was obtained from another function or a function argument that will need to be backtracked. Being familiar with how to find variables in memory is useful in case they need to be modified or to confirm its value.
