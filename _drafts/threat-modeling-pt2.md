---
title: "threat modeling for your digital life part 2: concepts"
subtitle: "before you learn to run, you must learn why you can't"
slug: threat modeling two
tags: threat-modeling
---
In [part 1]({% post_url 2021-08-19-threat-modeling-one %}), we talked about how to get started with threat modeling. You should now have a list of your assets, threats, and threat actors. However, a threat model is much use if you don't have mitigations to your threats.

However, before we get to the actual mitigations, you will need to understand some concepts when evaluating tools in order to select a tool appropriate for your threat model. You can scan the headings of this post and if you think you have a grasp on the concepts, feel free to move on to part 3 which will help you in your tool search.

I may mention specific products and services as examples. These are not endorsements unless explicitly stated. I'm also not paid to shill a specific product. 

# a word on trust 
Trust is an extremely important concept when it comes to evaluating your tools. You need to be able to trust a tool in some fashion before you make use of it. If you don't trust it, what's the point in using it? However, the big question is, how do you know if you can even trust a tool?

A lot of tools, especially commercial tools, will make exceptionally wild claims. You'll see the phrase "military grade encryption" thrown around a lot. However, remember that the military usually uses the lowest bidder. You'll also see things like "no logs", "zero knowledge", and "really fucking secure". 

It's almost impossible to fully trust something despite its claims. I highly recommend the article by Ken Thompson titled [Reflections on Trusting Trust (PDF)](https://www.cs.cmu.edu/~rdriley/487/papers/Thompson_1984_ReflectionsonTrustingTrust.pdf). This article may get a bit technical, but the point is that you have to trust someone at some point within the chain. If you can't, then you'll literally have to throw your electronic devices away. At this time, in computing, there is no such thing as zero trust, although you'll hear that word thrown around sometimes in marketing materials. 

The only way it's possible to fully trust something would be to build something from scratch. To quote Carl Sagan: "If you wish to make an apple pie from scratch, you must first invent the universe". This means that, unless you manufacture the hardware yourself and write your own firmware, you're gonna have to trust _somebody_.

# convenience vs. security
More often than not, unfortunately, security and convenience are diametrically opposing concepts. That is, the more secure something is, the less convenient it is and vice versa. Companies have been trying to marry the two with varying degrees of success, but no matter what it's a balancing act.

Based on your threat model, you're going to have to decide what you're willing to give up. Are you willing to give up convenience to be more secure, or do you not care about security in some aspects in order to achieve more convenience? 

For example, [Signal](https://www.signal.org/) is a very popular secure messaging app, and it's considered to be one of the most secure messaging apps. However, in the name of security, one feature of Signal is that it's not a simple process to [easily transfer messages](https://support.signal.org/hc/en-us/articles/360007059752-Backup-and-Restore-Messages) from one device to another such as when you purchase a new phone. This means that, unless you've taken specific steps to backup your Signal messages, if you lose your device all of your messages will be lost as well. Could you live with the fact that if your phone broke you'd likely lose all of your messages? Maybe your threat model will necessitate this functionality.

# commercial vs. free and open source tools
You have two choices when choosing a tool: commercial and and [free and open source (FOSS)](https://en.wikipedia.org/wiki/Free_and_open-source_software). My definition of a commercial tool is one that you have to pay for, although many commercial tools do have free offerings that may be adequate for your needs. Furthermore, commercial tools may open source their offerings, but the service itself may cost money to use unless you do something like self-host if that option is available.

FOSS tools are those whose [source code](https://en.wikipedia.org/wiki/Source_code) is open and available for all to use. Note that open source is not the same as free _and_ open source, but I'm not going to get into that philosophical debate here. Generally speaking, FOSS tools put the onus on the user to configure and use and may require a much more technical background to get up and running. 

## general advice
The following advice pertains to any type of tool you'll consider using. However, if one tool is missing something mentioned here does not automatically make it worse than another tool that has it. Again, remember, one of the biggest things you must consider when choosing a tool is trust. If you cannot trust your tool, you should not use it. You should never blindly trust a tool based on marketing alone.

### white papers, audits, and other docs
Most tools that advocate for or advertise security and privacy will have something called a [white paper](https://en.wikipedia.org/wiki/White_paper) available that describes, at a high level, their security practices for the tool or service that they're offering. Some entities may have even gone a step further and have been [audited](https://en.wikipedia.org/wiki/Information_technology_audit) by a **trustworthy** third party that can speak to the efficacy of their security controls. If a tool or service has either one of these, they will likely advertise them heavily. However, both white papers and audits can be technical in nature, so they may be difficult to understand for the average person. For an example, you can read [ProtonMail's audit](https://protonmail.com/blog/security-audit/) and [their white paper (pdf)](https://protonmail.com/docs/business-whitepaper.pdf) to get an idea of what content you can expect. 

Going one step further, some services even post their own threat models to help you decide whether or not you should use their service. Using ProtonMail as an example again, [here's their threat model](https://protonmail.com/blog/protonmail-threat-model/) which outlines what ProtonMail can and cannot help you protect against.

### known prior security and privacy issues
You should also check to see if the app or service has been exploited before. The simplest way to do this would be to simply search for "tool breach". For commercial entities, this can be relatively difficult unless the incident had been highly publicized. For open source tools, this may be a bit easier because there will generally be public information associated with these hacks. There's no such thing as a completely secure app or service; however, you should evaluate the severity of the breach and if the tool owner has learned from their mistakes. If they're consistently making amateur mistakes, then the app is not worth your time.

Furthermore, when an app or service is advertising privacy features, it should obviously abide by those tenets they hold. It's one thing if a service is breached. It's another that, through that breach, they lose data that they either claimed they never collected but had anyway, or it wasn't even secured properly. Ideally, if a service is breached, the data they lose is encrypted or secured in a fashion that makes the data rather useless. 

### privacy policies and terms of service 
If you want to take security and privacy seriously, you're going to have to actually read privacy policies and terms of service (ToS) agreements for any online services that you choose to use. Companies that take security seriously will have a simpler privacy policy because their business is security and privacy, so they will likely not collect a lot of data other than what's necessary. However, companies change their privacy policies and ToS agreements from time to time, so you'll need to keep up with those changes. 

### mergers and acquisitions
You should also watch out for mergers and acquisitions. From time to time, a tool will change hands, and the new owners may not be as keen on the privacy guarantees of the previous owner, or worse, they completely shut down the tool you were using. However, not all changes will be apparent right away. You may hear that a company will "remain independent" despite being acquired; however, this is mostly just marketing talk. Over time, what will likely happen is that the acquired tool's security or privacy features will be stripped away. It's very rare that an acquired tool will remain as it once was.

### interopability
When choosing a particular tool, you may have to consider interopability with other tools. Basically, this is evaluating how difficult it is to retrieve your data from the tool in order to transfer it to another tool. For example, a tool you're using is abandoned, it's acquired by a hostile company, or it just simply doesn't work for you anymore, so you need a way of exporting your data so you can import it into a new tool.

This can be very difficult depending on the tool.

## commercial software and services 
Evaluating commercial software and services is rather difficult because you generally have to take the company at its word that its doing what it says its doing. This is where white papers and third party audits come in handy, assuming the company has them. Without them, you're kinda reliant on their marketing materials which isn't ideal. Regardless, one needs to critically analyze a commercial tool's marketing materials. I mentioned this earlier, but you'll see marketing phrases like "military grade security", but that phrase doesn't really mean anything. Fun fact: you're using "military grade encryption" when you connect to secure websites via [HTTPS](https://en.wikipedia.org/wiki/HTTPS). However, generally speaking, commercial entities that advertise security and/or privacy as their primary product do not last very long in a market where trust is everything. That's not to say all companies that are currently alive are trustworthy, but it's something to think about.

Some commercial services actually do offer their source code for the apps that they offer. This is great because you can see the different issues that the app has via issue trackers like those on GitHub or GitLab, and it shows they're trying to go the extra mile in terms of earning your trust. 

Advantages:
+ known business model 
+ support 
+ (typically) less complex setup 

Disadvantages: 
+ cost money for better service or offerings 
+ must trust what they say is true
+ may still sell your private data 

## free and open source software
The concept of free and open source software (FOSS) is predicated on two things: free as in beer and free as in speech. Free as in beer is obvious--this means the software costs \$0, though some will say it's only \$0 if your time is worthless, but no one cares what these pedants have to say. Free as in speech may be weird concept when it comes to software. This basically means that you're allowed to take the software and do whatever you want with it. For example, if you find a tool that's really awesome but it doesn't do this one thing you want to do, you can download the source code, modify it to do what you want, and then distribute that software back. This is an oversimplification due to how software licenses work, but in general, this is how FOSS operates. 

Much like commercial software, you must trust in the development team of the FOSS app. Just because the code is open doesn't mean that it's automatically secure. Also, unless you're an experienced software engineer _and_ intimately familiar with the code base in question, it may be impossible for you to assess a given piece of software. One of the biggest misconceptions about FOSS is that it's more secure because "there are more eyes on it"; however, things can and do get missed like with [OpenSSL's](https://en.wikipedia.org/wiki/OpenSSL) [Heartbleed](https://heartbleed.com/) vulnerability which was identified in 2014, but it had existed since 2011. However, some have argued that the vulnerability was identified _because_ OpenSSL is open source. If OpenSSL was closed source, who knows how long this vulnerability would've gone unnoticed.

However, if you need absolute privacy, FOSS may be the way to go. It's (generally) not beholden to any corporate or government entities, and it doesn't have confusing ToSs or kilometer long privacy policies. If the software decides to add features you don't like, you can fork a previous version of the software to keep the undesired features out. FOSS offers almost unparalleled accessibility and customization, provided that you're skilled enough to do it.

This leads to FOSS's biggest weakness: most FOSS is a nightmare to use from a [UX](https://en.wikipedia.org/wiki/User_experience) perspective. This is primarily my opinion, but there's a lot of FOSS that was made by developers in a way that's intuitive to them and not for a typical user, so the difficulty curve of using it is almost a straight line. This has gotten better over time, but it's still a problem. For example, despite Linux distributions being more accessible than ever, they still have a long way to go to provide a consistent experience across hardware. 

If your threat model requires the use of secure FOSS tools, then you're going to have a lot of learning ahead of you if you're currently not a technical person. However, one other advantage of FOSS is that there are many communities out there that are willing to help you if you put in the effort of educating yourself first. 

Advantages: 
+ Many eyes on the code
+ Open about issues 
+ Can modify the code, if necessary 

Disadvantages: 
+ False sense of security because its open 
+ May be more difficult to use or configure 
+ May require more technical ability 

## "free" software 
I'm not going to talk too much about "free" offerings which are neither FOSS nor have a commercial offering. The reason for this is most things that advertise themselves as completely free are very likely a scam, or they harvest so much of your data that you'd be putting yourself at risk. For example, you may come across things like "free" VPNs with no commercial offering, but they're very likely logging all of your activity.

You may have no choice depending on your situation, and if that's the case, learn how to block the tool from the internet. 

# encryption 
[Encryption](https://www.cloudflare.com/learning/ssl/what-is-encryption/) is simply a way of scrambling data that makes it difficult or impossible to determine what the original text was supposed to be without having a password (also known as a key). This section isn't intended to be a primer on cryptography, but rather introduce you to the different forms of encryption that products will advertise. Note that there isn't one "superior" form of encryption, but good systems will use these multiple forms in concert. There are three different categories of encryption that are relevant to you: end-to-end encryption, encryption in transit, and encryption at rest. 

## end-to-end encryption (E2EE)
[End-to-end encryption (E2EE)](https://www.cloudflare.com/learning/privacy/what-is-end-to-end-encryption/) is probably the most misunderstood and misused term when describing an app's encryption capabilities. E2EE means that your data is encrypted on both endpoints of a message. For example, when you send a message in an app like Signal, before the message is sent, it is encrypted on your device, and it remains encrypted until it reaches its destination. 

To visualize this, imagine you have a lockbox which you put something inside of it, and you lock the lockbox with a key that only you and the recipient have. When you send that lockbox through the mail, it's impossible for the postal service to open or observe what's inside that lockbox without the key, so they just pass the lockbox along. Once it reaches its destination, the recipient can unlock the lockbox and retrieve what's inside. 

This is perfect because you only have to trust that the encryption used by the app is robust enough that someone in the middle cannot break it. You do not have to worry about someone inadvertently or purposefully eavesdropping on your message somewhere along the way before your message reaches its destination. 

The most important thing about E2EE is that you own your own encryption keys. That is, they're not generated and held by some third party that allows them to decrypt your data arbitrarily. While some apps may generate encryption keys transparently to ease the process, the keys are still located on the device in which the app was installed. This is important because if someone has access to your secret key, they can decrypt all of the messages on your device which makes the encryption useless. 

## encryption in transit 
Encryption in transit is often misunderstood as E2EE because data leaving your device is technically encrypted until it reaches its destination. However, the actual _contents_ of the message are not actually encrypted, but rather surrounded by a packet which conceals the message. For example, when you mail a letter, you wrap that letter in an envelope which "encrypts" the letter inside. That is, without opening the envelope, the transporter has no idea what the letter says, but if the transporter opened the envelope, the contents of the letter would be as plain as day. 

The biggest difference here is that it's possible for someone to intercept your message and read the contents of the message should the "envelope" be removed. This isn't as easy as it sounds, but it's still possible via attacks like a [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) attack. However, with the way protocols like HTTPS work, it's possible for your Internet Service Provider (ISP) to intercept your traffic and decrypt it. 

This may sound like I'm badmouthing encryption in transit, but it's important that you understand the risks when you're not using E2EE and only relying on transport encryption. 

## encryption at rest 
Encryption at rest simply means data is encrypted while it's currently not in use. For example, the files you send to your cloud provider may be encrypted at rest which means a third party cannot view what these files are. However, unless you own the encryption keys, these files can be viewed by the cloud provider storing your data. 

Encryption at rest is intended to prevent unauthorized third parties from viewing your data like in the case of a data breach. If an attacker compromises a cloud provider, they may only get away with encrypted blobs of data. However, your cloud provider is an "authorized" party, so they can view your files whenever they want. The cloud provider is technically the one with the encryption keys, so they can do what they want with your data. Granted, some providers will _claim_ they'll "never" look at your data, but they could if they want to. A provider may have policies in place, but a rogue employee could [simply ignore said policies](https://i.imgur.com/mSHi8.jpeg). 

If you do business with a cloud provider for data storage, they should advertise encryption at rest. If they don't, don't bother doing business with them as they don't take the minimum necessary precautions to keep your data safe. 


