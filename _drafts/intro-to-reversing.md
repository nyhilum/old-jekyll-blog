---
title: introduction to reverse engineering
subtitle: here we go again
slug: intro to reversing
---

# an introduction to reverse engineering
Reverse engineering is simply the art and science of taking something a part to see how that thing works. In meatspace, you may have taken a part a vehicle, a computer, a microwave, or some other device. You can do the same thing in cyberspace with software, but the process is a bit more complicated. That's not to say complicated hardware doesn't exist, but it's usually easier to wrap our heads around a physical object that exists rather than something that doesn't really exist. Thankfully, there are tools out there that will help you with this analysis process to make your life easier but they won't do the work for you. Much like taking a machine a part, you must understand how you took it a part in order to put it back together.

This post will focus on the reverse engineering of software on traditional computer desktop systems (Windows, Linux, MacOS) running Intel's x86 chipset. While all of the skills can transfer over to other areas, the tools won't necessarily follow.

# legality
Before jumping in too deep, we should talk about the legality of revesre engineering software a bit. I'm not a lawyer, so this isn't legal advice, and I will be speaking from a US perspective. non-US residents should check their local laws regarding this activity. If you're truly questioning the activities you're doing, you should really consult a lawyer.

Reverse engineering software in the US is very much a legal grey area because there's a lot of this-and-that and it's very context dependent based on what you're attempting to do. Reverse engineering something like malware will probably not land you in legal trouble as I doubt malware authors are going to come out of the woodwork to sue you. Reverse engineering corporate software, on the other hand, may land you in legal trouble depending on your reasons. 

I would recommend checking out the [EFF's FAQ](https://www.eff.org/issues/coders/reverse-engineering-faq) regarding reverse engineering in the US. They will do a better job of explaining it better than I can. 

# skills and knowledge
Before diving into the tools, it will be beneficial to go over the skillset required by prospective reverse engineers.

## programming
Some may tell you that no programming experience is necessary before getting into reversing; however, while this is true, it'll be an uphill battle in some cases. You don't have to be expert programmer with decades of experience, but you should at the very least understand control flow (`if` statements, loops), functions, and other programming paradigms. Basically, a programming 101 crash course will likely be enough. The language you have experience in or choose to learn doesn't really matter as any experience will be helpful to you.



I know I said language doesn't really matter, but the question will get asked anyway. If you want something relatively simple and high level, python is a good choice. However, python abstracts away a lot of under the hood stuff you'll see in your reversing journey, so it may not be the best in this case. That said, if you're new to programming it's not a bad choice. Lower level languages like C and C++ will give you a lot more insight as to how software works, but they're also a lot more difficult to learn especially if you're new.

## networking
In our connected world, knowing how networking works can be crucial to reversing a piece of software. Network connections from software are made all the time to do things like software license validation, anticheat processing, command and control, and so on. Understanding the networking functions an operating system uses will be critical to understanding how data is inserted into packets and then sent over the wire.

Disassembling and analyzing network packets is its own skill and it will require some dedication to fully understand the process.

# methodology
There are two ways of analyzing software: statically and dynamically. When reversing something, you will use a combination of both and rarely just one or the other. 

## static analysis
[Static analysis](https://en.wikipedia.org/wiki/Static_program_analysis) is the process of analyzing software as it exists on disk without executing the program. Within binary files, there's information stored about the binary that's needed by the operating system in order for it to run. The amount of information a binary has will vary depending on if the developer stripped symbols or not.


There are seemingly an infinite number of courses and books online that claim to teach you programming. I would personally avoid things like "learn X in Y days" because most of those are likely garbage. Pick a learning platform of your choice like Khan Academy, udemy, Pluralsight, ITPro.tv, YouTube, or your favorite and see if they have any programming related content that would be relevant to you. As far as books go, NoStarch Press has a ton of programming books that are written by various industry veterans that you'd have a hard time choosing a bad book.