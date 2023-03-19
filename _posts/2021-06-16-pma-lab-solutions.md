---
title: practical malware analysis lab solutions
subtitle: how i've anticipated your return to planet earth <!-- Thank You Scientist -- FXMLDR -->
slug: pma lab solutions
tags: labs malware reversing
---

This page documents the solutions to the labs found in the book [Practical Malware Analysis](https://nostarch.com/malware). While the solutions are found in the back of the book, I find it useful to write things in my own words for learning purposes.

I will be updating the page over time.

* toc
{:toc}

# chapter 15 -- anti-disassembly
This chapter focuses on anti-disassembly techniques used by malware programmers and how to defeat them.

## 15-1
This program's sole purpose is to take an argument and print "good job" if the secret code has been identified.

After identifying the `main()` function (`loc_401000`), we already see some disassembly errors noted by IDA.

![invalid jumps](/assets/pma_lab15-1_invalid-jmps.png)

Generally, disassemblers will evaluate the false path of a conditional jump first and trust that disassembled code. This sometimes causes the true path to be nonsensical when its evaluated if code is written in a particular manner. In the above image, you'll notice that the jump points to a byte inside the `call` instruction itself which isn't really valid.

To fix this problem, simply convert the `call` instruction into data and then convert the opcodes after 0xe8 into code. Once you covert that data into code, you may notice some other operations get turned into data. Simply continue down the list until no more data so you'll see something like the following.

![convert call](/assets/pma_lab15-1_call-convert1.png)

You'll need to repeat this process 4 more times when you see the broken `call` instructions. Once you're finished, you should see something like the following.

![all calls converted](/assets/pma_lab15-1_call-convert-all.png)

Now we're able to analyze the once obfuscated code blocks. You can ultimately ignore the `db 0E8h` lines, or you can convert them into `nop` instructions if they bother you--either way, they aren't used.

Each code block appears to be moving the user's input into a register and then comparing that register's value to some hex value. If you know your ASCII table, you'll know that the values being compared are the characters 'p' (0x70), 'q' (0x71), and 'd' (0x64). 

If you attempted to enter the characters in the order in which they're seen in (i.e. pqd), you'll fail the challenge. This is because the second code block is comparing the third character of the user string with the password. Therefore, the correct order for the password is 'pdq'.

[\[back to top\]](#)

## 15-2
The `main()` function starts at `loc_401000`. Scrolling down, broken disassembly can be found starting at `401163`.

![broken disassembly](/assets/pma_lab15-2_img1_broken-assembly.png)

Converting this data to code gives us the following output:

![fixed disassembly](/assets/pma_lab15-2_img2_fixed-assembly.png)

Note that I also converted the `jmp` above this block into data as it's not really important. In this data-now-code, we see a call to `sub_401386`. Opening this function gives us a simple function which appears to be building string one character at a time. Converting the hex values into characters gives us the following URL: `hxxp://www.practicalmalwarenalysis.com/bamboo.html`

To determine how the `user-agent` is generated, we need to know what function actually makes use of this field. There are a couple of function calls to Windows API functions like `InternetOpenA` and `InternetOpenURLA`, so it's only a matter of seeing which one actually makes use of the `user-agent`. Searching the internet for [InternetOpenA](https://docs.microsoft.com/en-us/windows/win32/api/wininet/nf-wininet-internetopena) gives us the following arguments:

```
void InternetOpenA(
  LPCSTR lpszAgent,
  DWORD  dwAccessType,
  LPCSTR lpszProxy,
  LPCSTR lpszProxyBypass,
  DWORD  dwFlags
);
```

Reading the documentation, the parameter `lpszAgent` is used as the `user-agent` in HTTP.

```
.text:0040113F loc_40113F:                             ; CODE XREF: .text:00401098↑j
.text:0040113F                                         ; .text:004010B0↑j
.text:0040113F                 push    0
.text:00401141                 push    0
.text:00401143                 push    0
.text:00401145                 push    1
.text:00401147                 lea     edx, [ebp-100h]
.text:0040114D                 push    edx ;pointer to user-agent string
.text:0040114E                 call    ds:InternetOpenA
```

Since arguments are pushed in reverse order (right to left), the value pointed to by `edx` (`[ebp-100]`) is going to be the `user-agent` string. 

```
.text:00401047 loc_401047:                             ; CODE XREF: .text:0040103D↑j
.text:00401047                 push    100h
.text:0040104C                 lea     ecx, [ebp-100h]
.text:00401052                 push    ecx
.text:00401053                 call    ds:gethostname
.text:00401059                 test    eax, eax
.text:0040105B                 jnz     short loc_401073
.text:0040105D                 push    offset aNotEnoughName ; "not enough name"
.text:00401062                 call    ds:printf
.text:00401068                 add     esp, 4
.text:0040106B                 or      eax, 0FFFFFFFFh
.text:0040106E                 jmp     loc_401308
```

We see a call to [gethostname](https://docs.microsoft.com/en-us/windows/win32/api/winsock/nf-winsock-gethostname) which takes a `char` pointer that will be used to hold the hostname of the machine and a second argument which states how long the buffer should be (0x100 in this case). After the call, `ecx` will contain the pointer to the buffer which contains the hostname.

The blocks of code that follow are ultimately taking the hostname and obfuscating it by adding 1 to every character in the hostname string with some exceptions. If the character is Z or 9, the loop rolls over to A or 0, respectively.

When the target web page has been downloaded, the program then searches for the string "Bamboo::" and then it searches again for "::". 

![string search in html](/assets/pma_lab15-2_img3_bamboo.png)

After the above block of code, we see a `jmp` which appears to jump into itself and then some nonsensical data which happens after the jump.

![jump into self](/assets/pma_lab15-2_img4_jmp-self.png)

The jump is technically invalid, so converting it to data then converting the byte after the jump opcode into code reveals new information.

![fixed jump into self](/assets/pma_lab15-2_img5_jmp-fixed.png)

However, again, we see some more nonsense, so we just repeat our process of turning code into data and then back again to reveal some new instructions. One interesting instruction is the call to `sub_40130f` which, like `sub_401386`, simply sets up a string called "Account Summary.xls.exe". After the file is retrieved, it's executed in a shell as noted by `ShellExecuteA` and the malware is now running on the machine.

[\[back to top\]](#)

## 15-3
This piece of malware attempts to be tricky by disguising itself as a typical program. Running the application shows it to be a very basic process monitor. However, opening the program in IDA shows a call to `URLDownloadToFileA` which seems suspicious.

We need to find where the malware starts its execution. At first glance, there doesn't seem to be a valid code path that gets to the function call `URLDownloadToFileA`. The start of the `main()` function, we see the typical function prologue stuff and then some odd instructions. 

```
.text:0040100C                 mov     eax, 400000h
.text:00401011                 or      eax, 148Ch
.text:00401016                 mov     [ebp+4], eax
```

The value 0x400000 is moved into `eax` and then `or`ed with 0x148c gives you 0x40148c and then this value is placed into `[ebp+4]`. Using IDA's stack frame view to see that `[ebp+4]` is going to be the return address when the `main()` function finishes. Navigating to this address gives us the start of some other code. The code is broken and needs to be fixed up, but after fixing it it doesn't appear necessarily malicious at first glance.

```
.text:00401497 loc_401497:                             ; CODE XREF: .text:00401494↑j
.text:00401497                 push    offset loc_4014C0
.text:0040149C                 push    large dword ptr fs:0
.text:004014A3                 mov     large fs:0, esp
.text:004014AA                 xor     ecx, ecx
.text:004014AC                 div     ecx
.text:004014AE                 push    offset aForMoreInforma ; "For more information please visit our w"...
.text:004014B3                 call    printf
.text:004014B8                 add     esp, 4
.text:004014BB                 pop     edi
.text:004014BC                 pop     esi
.text:004014BD                 pop     ebx
.text:004014BE                 pop     ebp
.text:004014BF                 retn
``` 

One thing that stands out is that this code appears to be messing with the [thread environment block (TEB)](https://en.wikipedia.org/wiki/Win32_Thread_Information_Block) by adding its own code to be picked up by the exception handler when an exception occurs. It then forces an exception by causing a divide by zero, so the code after the `div` instruction doesn't actually execute. Once the exception occurs, the code will jump to 0x4014c0.

Again, the disassembly is broken at this address, so it needs to be fixed up by converting data to code and code to data like we have been doing. The resulting code ends up as the following.

```
.text:004014C0 loc_4014C0:                             ; DATA XREF: .text:loc_401497↑o
.text:004014C0                 mov     esp, [esp+8]
.text:004014C4                 mov     eax, large fs:0
.text:004014CA                 mov     eax, [eax]
.text:004014CC                 mov     eax, [eax]
.text:004014CE                 mov     large fs:0, eax
.text:004014D4                 add     esp, 8
.text:004014D4 ; ---------------------------------------------------------------------------
.text:004014D7                 db 0EBh
.text:004014D8 ; ---------------------------------------------------------------------------
.text:004014D8                 inc     eax
.text:004014DA                 dec     eax
.text:004014DB                 call    $+5
.text:004014E0                 push    ebp
.text:004014E1                 mov     ebp, esp
.text:004014E3                 push    ebx
.text:004014E4                 push    esi
.text:004014E5                 push    edi
.text:004014E6                 push    offset unk_403010
.text:004014EB                 call    sub_401534
.text:004014F0                 add     esp, 4
.text:004014F3                 push    offset unk_403040
.text:004014F8                 call    sub_401534
.text:004014FD                 add     esp, 4
.text:00401500                 push    0
.text:00401502                 push    0
.text:00401504                 push    offset unk_403040
.text:00401509                 push    offset unk_403010
.text:0040150E                 push    0
.text:00401510                 call    URLDownloadToFileA
.text:00401515                 jz      short loc_40151A
.text:00401517                 jnz     short loc_40151A
```

In this block, we see 2 calls to the same function, `sub_401534` but with different arguments. The arguments seem to be strings of seemingly random data. Inside the function, we see a loop with an `xor` operation which indicates that this loop is likely decrypting those random strings of data.

```
.text:00401534 sub_401534      proc near               ; CODE XREF: .text:004014EB↑p
.text:00401534                                         ; .text:004014F8↑p
.text:00401534
.text:00401534 arg_0           = dword ptr  8
.text:00401534
.text:00401534                 push    ebp
.text:00401535                 mov     ebp, esp
.text:00401537
.text:00401537 loc_401537:                             ; CODE XREF: sub_401534+26↓j
.text:00401537                 mov     eax, [ebp+arg_0]
.text:0040153A                 movsx   ecx, byte ptr [eax]
.text:0040153D                 test    ecx, ecx
.text:0040153F                 jz      short loc_40155C
.text:00401541                 mov     edx, [ebp+arg_0]
.text:00401544                 movsx   eax, byte ptr [edx]
.text:00401547                 xor     eax, 0FFh
.text:0040154C                 mov     ecx, [ebp+arg_0]
.text:0040154F                 mov     [ecx], al
.text:00401551                 mov     edx, [ebp+arg_0]
.text:00401554                 add     edx, 1
.text:00401557                 mov     [ebp+arg_0], edx
.text:0040155A                 jmp     short loc_401537
.text:0040155C ; ---------------------------------------------------------------------------
.text:0040155C
.text:0040155C loc_40155C:                             ; CODE XREF: sub_401534+B↑j
.text:0040155C                 pop     ebp
.text:0040155D                 retn
```

Decrypting the 2 strings gives us:
* `hxxp://www.practicalmalwareanalysis.com/tt.html`
* `spoolsrv.exe`

The first string is a web address which is used to pull down data and it writes that data into a file named spoolsrv.exe to do something malicious.

After this has been completed, the malware terminates by using `ExitProcess`.

[\[back to top\]](#)

# chapter 16 -- anti-debugging
This chapter primarily focused on anti-debugging techniques used by Windows programs.

## 16-1
Since we know this program has anti-debugging baked in, I decided to open the program in IDA first. The `main()` function was determined to start in `sub_403530()`.

Within the main function, we see some instructions which are accessing the [process environment block (PEB)](https://en.wikipedia.org/wiki/Process_Environment_Block).

```
.text:00403540                 mov     [ebp+var_1824], 0
.text:0040354A                 mov     [ebp+var_1828], 0
.text:00403554                 mov     eax, large fs:30h
.text:0040355A                 mov     bl, [eax+2] ;BeingDebugged
.text:0040355D                 mov     [ebp+var_1820], bl
.text:00403563                 movsx   eax, [ebp+var_1820]
.text:0040356A                 test    eax, eax
.text:0040356C                 jz      short loc_403573
.text:0040356E                 call    sub_401000
;...
.text:00403573                 mov     eax, large fs:30h
.text:00403579                 mov     eax, [eax+18h]
.text:0040357C                 db      3Eh
.text:0040357C                 mov     eax, [eax+10h] ;ForcedFlags
.text:00403580                 mov     [ebp+var_1824], eax
.text:00403586                 cmp     [ebp+var_1824], 0
.text:0040358D                 jz      short loc_403594
.text:0040358F                 call    sub_401000
;...
.text:00403594                 mov     eax, large fs:30h
.text:0040359A                 db      3Eh
.text:0040359A                 mov     eax, [eax+68h] ;NtGlobalFlags
.text:0040359E                 sub     eax, 70h
.text:004035A1                 mov     [ebp+var_1828], eax
.text:004035A7                 cmp     [ebp+var_1828], 0
.text:004035AE                 jnz     short loc_4035B5
.text:004035B0                 call    sub_401000
```

Each of these different blocks accesses a different part of the PEB. These different parts contain different types of information to state whether or not a process is being debugged. In order from top to bottom the program is looking for
* `BeingDebugged` flag at PEB+0x2 with a non-zero value
* `ForceFlags` at PEB+0x18+0x10 with a non-zero value
* `NtGlobalFlags` at PEB+0x68 with a value of 0x70

If the test fails, then `sub_401000` is called which causes the program to immediately stop and delete itself from the machine. There are a few ways to avoid the anti-debugging measures:
* manually patch all of the jump instructions
* replace the `call`s to `sub_401000` with `nop`
* manually modify the PEB structure locations referenced by the program
* use a plugin that aids in anti-anti-debugging

Manually patching instructions would likely take a while unless you wrote a script to patch the program because the jumps and calls are sprinkled throughout the program itself, so while this is possible, it would be tedious.

Modifying the PEB would be another option as long as you identified the proper place in memory where the flags are set. We know that the PEB starts at `fs:[0x30]`, so in x32dbg's dump window, we can navigate to that address by pressing `ctrl+g` then entering that value in the box.

![x64dbg peb dump](/assets/pma_lab16-1_img1_x32dbg.png)

Once you identify the PEB, you can start clearing values in the PEB to prevent the checks from occurring. This is as simple as clearing the values in memory and setting them to 0. The only exception is the `ForcedFlags` field. Instead, you set the pointer to the `ProcessHeap` array to 0.

Alternatively, you can avoid all of this suffering and just install a plugin called [ScyllaHide](https://github.com/x64dbg/ScyllaHide), or a plugin of your choice if you know of a better one. This will basically do what we did manually to the PEB plus a lot of extra options.

[\[back to top\]](#)
## 16-2
This binary is more of a CTF than it is an example of a malicious payload.

Running the binary in the command line, we get the following output:

```
.\Lab16-02.exe
usage: .\Lab16-02.exe <4 character password>
```

As one would expect, inputting an incorrect password simply tells you to try again. The hard part now is trying to determine what the password is. Again, the best first step is to identify the `main()` function which is `sub_4011e0`. Skipping over the function prologue we see the following block:

```
.tls:0040120A loc_40120A:                             ; CODE XREF: sub_4011E0+11↑j
.tls:0040120A                 lea     edx, [ebp+ThreadId]
.tls:0040120D                 push    edx             ; lpThreadId
.tls:0040120E                 push    0               ; dwCreationFlags
.tls:00401210                 push    0               ; lpParameter
.tls:00401212                 push    offset StartAddress ; lpStartAddress
.tls:00401217                 push    0               ; dwStackSize
.tls:00401219                 push    0               ; lpThreadAttributes
.tls:0040121B                 call    ds:CreateThread
.tls:00401221                 push    3E8h            ; dwMilliseconds
.tls:00401226                 call    ds:Sleep
.tls:0040122C                 push    4
.tls:0040122E                 push    offset byte_408030
.tls:00401233                 mov     eax, [ebp+arg_4]
.tls:00401236                 mov     ecx, [eax+4]
.tls:00401239                 push    ecx
.tls:0040123A                 call    sub_402110
.tls:0040123F                 add     esp, 0Ch
.tls:00401242                 mov     [ebp+var_4], eax
.tls:00401245                 cmp     [ebp+var_4], 0
.tls:00401249                 jnz     short loc_40125A
```

Here we see a few function calls. I skipped over the library calls and decided to investigate the lines starting with `offset`. Double clicking on `StartAddress` gives you a rather complicated that seems to be performing some sort of decoding or decryption.

```
.tls:00401099                 xor     eax, eax
.tls:0040109B                 mov     bl, byte_40A968
.tls:004010A1                 or      al, 1
.tls:004010A3                 shl     al, 2
.tls:004010A6                 mov     byte_408033, al
.tls:004010AB                 mov     byte_408032, 7
.tls:004010B2                 mov     byte_408030, 9
.tls:004010B9                 dec     al
.tls:004010BB                 mov     byte_408031, al
.tls:004010C0                 or      al, 2
.tls:004010C2                 mov     byte_408033, al
.tls:004010C7                 mov     byte_408031, 0Bh
.tls:004010CE                 add     al, 0Bh
.tls:004010D0                 mov     byte_408033, al
;...
```

It seems here that 4 variables are made use of here with their values: 
* `byte_408030` = 0x50
* `byte_408031` = 0x7d
* `byte_408032` = 0xff
* `byte_408033` = 0xff

One other interesting thing about this function is that it accesses the PEB and moves the `BeingDebugged` flag into `bl` for use later in the decoding routine. This means that the final decoded value will be different depending on if the program is being debugged or not.

```
.tls:0040112B                 mov     ebx, large fs:30h ;assign peb pointer to ebx
;...
.tls:0040118B                 mov     bl, [ebx+2] ;store BeingDebugged flag into bl
.tls:0040118E                 rol     byte_408031, 1
.tls:00401194                 xor     byte_408033, 80h
.tls:0040119B                 rol     byte_408033, 7
.tls:004011A2                 add     byte_408032, bl ;add BeingDebugged flag to this variable
```

Now we're presented with two options: we can attempt to write our own script based on the disassembly to recreate the decoded string, or we can run the program in a debugger and let the program do the work. Since I'm lazy, I decided to do the latter.

> The main debugger used by this book is OllyDbg. I prefer x64dbg, but I'll use OllyDbg 2.0 in this case since the questions specify OllyDbg.

Upon attempting to load the program into OllyDbg, the program terminates immediately. This tells us that something is preventing the program from running if being debugged by OllyDbg. I reviewed the disassembly in IDA pro and noticed that the section was prefixed with `.tls` which indicates that this program is likely using [thread-local storage (TLS)](https://en.wikipedia.org/wiki/Thread-local_storage) callbacks (not to be confused with transport layer security). I opened the binary in CFF explorer to confirm the presence of the TLS callback section.

![cff explorer tls callback section](/assets/pma_lab16-2_img1_cff.png)

As far as we're concerned, the important part of about TLS callbacks is that they're executed _before_ a program's entry point is hit. Most debuggers break on a program's entry point by default, so this TLS section is free to execute code before the debugger automatically stops the program.

Most modern debuggers should have the option to break on TLS callbacks, but some do not do it by default. x64dbg, I believe, is configured to do this by default, but OllyDbg 2.0 is not and OllyDbg prior to version 2 doesn't have this option at all. To configure it in OllyDbg, go to Options -> Debugging -> Start and select "TLS callback (if defined)". This will cause OllyDbg to stop at the first TLS callback it finds.

After configuring OllyDbg properly, restart the program. Luckily, we see that OllyDbg doesn't just quit this time. Instead, execution has been paused at the start of the TLS callback function. Almost immediately, we can see suspicious code:

![ollydbg tls callback ollydbg detection](/assets/pma_lab16-2_img2_tls-callback.png)

This code is using [FindWindowA](https://docs.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-findwindowa) to see if OllyDbg is running. If this check succeeds, the program quits. In order to bypass this check, we can patch the `jz` instruction to `jmp` so the jump is always taken.

After modifying the jump instruction, I set a breakpoint at 0x4011e0 which is the `main()` function simply to stop the execution. I then set another breakpoint after 0x40122c so the variable at 0x408030 gets populated.  The comments column is then populated with the string "bzqrp@ss" at 0x40122e.

![ollydbg incorrect password](/assets/pma_lab16-2_img3_olly-wrong-pass.png)

We see this string and 2 other arguments get pushed to the stack and then the function `sub_402110` is called. The other 2 arguments are the number 4 and the argument that was supplied to the command line. Based on the 3 arguments and the content of the function, we can infer that the function being called is `strncmp`. Now we know where the comparison between the password and the user supplied argument is being performed. 

However, the password we recovered earlier was more than 4 characters--"bzqrp@ss". `strncmp` takes 3 arguments where one of which says how many characters are to be compared. The 4 being pushed onto the stack says to only compare the first 4 characters of the 2 strings. Therefore, we can ignore the last 4 characters of the password "p@ss". That means we can assume the password is "bzqr", right?

However, typing in "bzqr" in our command line gives us an invalid password error. Why is this? Recall that there was some functionality in the `StartAddress` function to return a different value based on whether or not the current program is being debugged. We can now assume that "bzqr" is not actually correct.

To bypass this problem, we will need to modify the PEB `BeingDebugged` member to 0 or just use an anti-anti-debugging plugin. I personally opted for the plugin since it's easier. After installing the plugin and rerunning the program, we get back down to the string again and we see the value "bzrrp@ss" instead of "bzqrp@ss".

![x32dbg showing right password](/assets/pma_lab16-2_img4_x32dbg-pass.png)

This means that the correct password is "bzrr".

![successful password usage](/assets/pma_lab16-2_img5_success.png)

[\[back to top\]](#)

## 16-3
This malware is similar to lab 9-2 with some anti-debugging and other modifications.

Running strings on the binary gives us a lot of junk, some library functions, and some strings relating to `cmd.exe`. It looks like there's a `/c del` command in there, so the malware is likely going to delete something. Trying to run the malware makes a `cmd` window flash and then disappear--nothing seems to have happened. 

Opening the program and identifying the program's `main()` function, `sub_4013d0`, shows the use of a stack string. I know this is a stack string because the hex values here fall within the ASCII range.

![ida stack string](/assets/pma_lab16-3_img1_stack-string.png)

Converting these values to characters gives us the following strings:
* 1qbz2wsx3edc
* ocl.exe

After a few string operations, we see a call to `sub_4011e0`. On first glace, this function seems to do something with performance as there are calls to `QueryPerformanceCounter`. This function is used, in this case, as an attempt to identify a debugger by estimating how much time should pass between 2 or more calls to `QueryPerformanceCounter`. Generally, the programmer will estimate how much time should pass between calls, and if it's outside of that range, the program will do something else. 

However, in between performance counter calls, the code is forcing a divide by zero exception to occur which will cause all debuggers to pause execution. This pause would cause enough time to elapse to allow the code to detect the presence of a debugger using its own predefined time constraints.

```
;...
.text:0040121D    call    ds:QueryPerformanceCounter ;1st counter
;...
.text:00401229    xor     ecx, ecx ;zero out ecx
;...
.text:00401234    push    eax
.text:00401235    push    large dword ptr fs:0 
.text:0040123C    mov     large fs:0, esp ;modify exception handler
.text:00401243    div     ecx ;divide by zero
;...
.text:00401271    call    ds:QueryPerformanceCounter ;2nd counter
.text:00401277    mov     edx, dword ptr [ebp+var_110]
.text:0040127D    sub     edx, dword ptr [ebp+PerformanceCount]
.text:00401280    mov     [ebp+var_114], edx
.text:00401286    cmp     [ebp+var_114], 4B0h ;within timeframe?
.text:00401290    jle     short loc_40129C
.text:00401292    mov     [ebp+var_118], 2 ;time constraint missed, at 2 to variable
;...
```

I tried a few different configurations to make my debugger run fast enough so that it doesn't hit the time constraints without modifying the code itself, but I wasn't able to make that happen. Further reading suggests that even though I configured the debugger to automatically pass the exception to the program that debuggers handle exceptions much more slowly than if the program were running naturally. Also, it's very likely my VM is simply not performant enough while my debugger is running.

Ultimately, I figured out what happens when the timing check isn't successful. The program sets a variable located at `[ebp-118]` to 2 instead of its default value of 1. This has a direct impact on the subsequent loop that occurs later when it manipulates the string `ocl.exe`.

```
.text:004011F6    mov     [ebp+var_118], 1 ;set to 1
;...
.text:00401271    call    ds:QueryPerformanceCounter
.text:00401277    mov     edx, dword ptr [ebp+var_110]
.text:0040127D    sub     edx, dword ptr [ebp+PerformanceCount]
.text:00401280    mov     [ebp+var_114], edx
.text:00401286    cmp     [ebp+var_114], 4B0h
.text:00401290    jle     short loc_40129C ;performance check fails
.text:00401292    mov     [ebp+var_118], 2 ;set to 2 for failing
;...
.text:004012B7 loc_4012B7:                             ; CODE XREF: sub_4011E0+C6↑j
.text:004012B7    cmp     [ebp+var_11C], 3
.text:004012BE    jge     short loc_4012EA
.text:004012C0    mov     ecx, [ebp+var_11C]
.text:004012C6    add     ecx, 1
.text:004012C9    imul    ecx, [ebp+var_118] ;multiply by 1 or 2
```

When this function finishes, you're left with one of two strings: `qgr.exe` or `peo.exe`. `qgr.exe` is what you get when you fail the timing checks, and `peo.exe` is what you get if you pass them. This is important for the `strncmp` (`sub_4017b0`) function call which compares the binary's current name with the transformed name. The 2 values have to match or else the program will terminate. Therefore, the binary should be renamed to `peo.exe` in order to allow the malware to run correctly when not under a debugger. You could change the binary's name to `qgr.exe` while debugging it so you do not have to worry about patching the program during a debugging session, but when the malware runs for real, it will need to have the name `peo.exe`, so knowing its real name is important for when you list out the indicators of compromise.

![executable name failed timing check](/assets/pma_lab16-3_img2_failed-timing-check.png)

![executable name successful timing check](/assets/pma_lab16-3_img3_success-timing-check.png)

A couple of blocks of code down we see 2 calls to `GetTickCount` and a call to `sub_401000`. Inside `sub_401000`, we see another forced exception to slow down performance of the debugger, so when the second `GetTickCount` is called the timing will fail and the program will quit. Analyzing `sub_401000` shows that it doesn't do anything important except to force an exception, so this function can be patched out to avoid failing the timing check. Alternatively, the `jbe` can be patched to `jmp` so the jump is always taken.

```
loc_401584:
call    ds:GetTickCount
mov     [ebp+var_2B4], eax
call    sub_401000 ;forces an exception, can be patched out
call    ds:GetTickCount
mov     [ebp+var_2BC], eax
mov     ecx, [ebp+var_2BC]
sub     ecx, [ebp+var_2B4]
cmp     ecx, 1
jbe     short loc_4015B7 ;alt. change to jmp
```

The next block of code, if you passed the timing checks, includes a call to `sub_401300`. This subroutine, again, includes another forced exception and then the `rdtsc` instruction. `rdtsc` is an instruction that counts the time since last system reboot, so using it twice will give you a difference in time between a given set of instructions.

In this situation, when the timing checks fail, `sub_4010e0` is called. The long and short of this function's purpose is to delete the malware if the timing checks fail.

```
;sub_4010e0
push    104h            ; nSize
lea     eax, [ebp+Filename]
push    eax             ; lpFilename
push    0               ; hModule
call    ds:GetModuleFileNameA
push    104h            ; cchBuffer
lea     ecx, [ebp+Filename]
push    ecx             ; lpszShortPath
lea     edx, [ebp+Filename]
push    edx             ; lpszLongPath
call    ds:GetShortPathNameA
mov     edi, offset aCDel ; "/c del "
lea     edx, [ebp+Parameters]
;...
push    0               ; nShowCmd
push    0               ; lpDirectory
lea     eax, [ebp+Parameters]
push    eax             ; lpParameters
push    offset File     ; "cmd.exe"
push    0               ; lpOperation
push    0               ; hwnd
call    ds:ShellExecuteA
push    0               ; uExitCode
```

If the timing checks are successful, this subroutine then takes the string `1qbz2wsx3edc` and uses it to decode the seemingly junk string that's stored at 0x40604c.

![junk string at 40604c](/assets/pma_lab16-3_img4_junk-string.png)

```
loc_401397:
cmp     [ebp+var_C], 1Ch
jge     short loc_4013C0
mov     ecx, [ebp+arg_4] ;junk string at 0x40604c
add     ecx, [ebp+var_C]
movsx   ecx, byte ptr [ecx]
mov     eax, [ebp+var_C]
cdq
idiv    [ebp+var_8]
mov     eax, [ebp+arg_0]
movsx   edx, byte ptr ds:(loc_40132B - 40132Bh)[eax+edx]
xor     ecx, edx
mov     eax, [ebp+arg_8] ;1qbz2wsx3edc
add     eax, [ebp+var_C]
mov     byte ptr ds:(loc_40132B - 40132Bh)[eax], cl
jmp     short loc_40138E
```

I allowed the malware to execute this loop for me so I didn't have to decode it manually. Ultimately, the junk string was transformed into the URL `adg.malwareanalysisbook.com`.

Since this exercise was to simply identify the anti-debugging techniques and to bypass them, I'll stop here, but there is additional functionality if you'd like to investigate further.

[\[back to top\]](#)



