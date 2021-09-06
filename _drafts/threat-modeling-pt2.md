---
title: "threat modeling for your digital life part 2: concepts"
subtitle: "before you learn to run, you must learn why you can't"
slug: threat modeling two
tags: threat-modeling
---
In [part 1]({% post_url 2021-08-19-threat-modeling-one %}), we talked about how to get started with threat modeling. You should now have a list of your assets, threats, and threat actors. However, a threat model is much use if you don't have mitigations to your threats.

However, before we get to the actual mitigations, you will need to understand some concepts when evaluating tools in order to select a tool appropriate for your threat model. You can scan the headings of this post and if you think you have a grasp on the concepts, feel free to move on to part 3 which will help you in your tool search.

# a word on trust 
Trust is an extremely important concept when it comes to evaluating your tools. You need to be able to trust a tool in some fashion before you make use of it. If you don't trust it, what's the point in using it? However, the big question is, how do you know if you can even trust a tool?

A lot of tools, especially commercial tools, will make exceptionally wild claims. You'll see the phrase "military grade encryption" thrown around a lot. However, remember that the military usually uses the lowest bidder. You'll also see things like "no logs", "zero knowledge", and "really fucking secure". That last one may not actually be used. 

It's almost impossible to fully trust something despite its claims. I highly recommend the article by Ken Thompson titled [Reflections on Trusting Trust (PDF)](https://www.cs.cmu.edu/~rdriley/487/papers/Thompson_1984_ReflectionsonTrustingTrust.pdf). This article may get a bit technical, but the point is that you have to trust someone at some point within the chain. If you can't, then you'll literally have to throw your electronic devices away. At this time, in computing, there is no such thing as zero trust, although you'll hear that word thrown around sometimes in cybersecurity circles.

The only way it's possible to fully trust something would be to build something from scratch. This brings to mind the Carl Sagan quote "If you wish to make an apple pie from scratch, you must first invent the universe". This means that, unless you manufacture the hardware itself and write your own firmware, you're gonna have to trust _somebody_.

With that out of the way, let's get on with the post.

# convenience vs. security
More often than not, unfortunately, security and convenience are diametrically opposing concepts. That is, the more secure something is, the less convenient it is and vice versa. Companies have been trying to marry the two with varying degrees of success, but no matter what it's a balancing act.

Based on your threat model, you're going to have to decide what you're willing to give up. Are you willing to give up convenience to be more secure, or do you not care about security in some aspects in order to achieve more convenience? 

For example, [Signal](https://www.signal.org/) is a very popular secure messaging app, and it's considered to be one of the most secure messaging apps. However, in the name of security, one feature of Signal is that it's not possible to [easily transfer messages](https://support.signal.org/hc/en-us/articles/360007059752-Backup-and-Restore-Messages) from one device to another such as when you purchase a new phone. Unlike other messaging apps, Signal stores none of your data on their servers, so installing Signal on a new device is not simply enough for messages to transfer.

# commercial, "free", and free and open source tools
When it comes to tools, you have three choices when choosing a tool: commercial, free, and free and open source. Each one of these have their own sets of advantages and disadvantages.

## general advice
The following advice pertains to any type of tool you'll consider using. However, if one tool is missing something mentioned here does not automatically make it worse than other tool that has it. Again, remember, one of the biggest things you must consider when choosing a tool is trust. If you cannot trust your tool, you should not use it. You should never blindly trust a tool based on marketing alone. The advice here is to help you perform your own research so you can establish trust.

Most tools that are used for security purposes will generally have something called a [white paper](https://en.wikipedia.org/wiki/White_paper) available that describes, at a high level, their security practices for the tool or service that they're offering. Some entities may have even gone a step further and obtained an [audit](https://en.wikipedia.org/wiki/Information_technology_audit) from a trustworthy third party that can speak to the efficacy of their security controls. If a tool or service has either one of these, they will likely advertise them heavily.

When evaluating a tool for security, you should also check to see if the app or service has been exploited before. The simplest way to do this would be to simply search for "\[tool\] breach". For commercial entities, this can be very difficult unless it's been highly publicized. For open source tools, this may be a bit easier because there will generally be public information associated with these hacks. There's no such thing as a perfect app or service; however, you should evaluate the severity of the breach and if the tool owner has learned from their mistakes. If they're consistently making amateur mistakes, then the app is not worth your time. 

If you want to take security and privacy seriously, you're going to have to actually read privacy policies and terms of service (ToS) agreements. More often than not, companies that take security seriously will have a simpler privacy policy because they have no way of monetizing your stuff, and they will just have no interest in doing so. However, you must also place trust in what they've written is truthful, and that they're unlikely to change their policy later down the line. Companies change their privacy policies and ToS agreements from time to time, so you'll need to keep up with those changes.

## commercial
Commercial tools and services are those that cost money to use. This may or may not be an option for some people depending on their budget. A lot of commercial tools will have a free tier, but that free tier may not be adequate for your use case. 

Regardless, one needs to critically analyze a commercial tool's marketing materials. I mentioned this earlier, but you'll see marketing phrases like "military grade security", but that phrase doesn't really mean anything in the end. Fun fact: you're using "military grade encryption" when you connect to secure websites. I personally would likely avoid services that use this particular phrase, but that's just me.

Furthermore, as stated before, you must evaluate a commercial entity's privacy policy. Just because you're paying for a service doesn't mean they aren't selling your data as a side hustle. 

Advantages:
+ known business model 
+ support 
+ less complex setup 

Disadvantages: 
+ cost money 
+ must trust what they say is true
+ may still sell your private data 

## "free"
You've likely heard the phrase "if you're not paying, you're the product" when it comes to free products. This is generally true, especially for free services like VPNs. Again, this is where you having to read the privacy policy or terms of service comes into play. The only way to know what the service is doing is by reading those policies. You also have to trust that what they write in these policies is truthful. 

Advantages:
+ zero monetary cost 

Disadvantages:
+ likely highly invasive
+ very likely selling your data
+ trust is paramount 

## free and open source software








