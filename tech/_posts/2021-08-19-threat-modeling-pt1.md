---
title: "threat modeling for your digital life part 1: an introduction"
subtitle: "just because you're paranoid doesn't mean they aren't out to get you"
slug: "threat modeling one"
tags: threat-modeling 
---

Threat modeling is a process used by cybersecurity professionals to determine what is likely to pose a threat to a software system. However, threat modeling is also used every day by most people without them even realizing it. For example, when the weather is bad outside, you may weigh the option to go into work or not, assuming you have that option. 

This post will introduce threat modeling for a normal human's digital life as opposed to a software system. In future posts, we will also talk about the tools, techniques, and procedures that can aid you in your threat modeling process.

In general, a threat model will be composed of the assets you want to protect and the threats associated with those assets. For example, SMS (text messages) are almost always intended to be private, but they can be easily intercepted by your carrier, the government, or if someone SIM clones your phone. To address this threat, you'd implement a secure messaging app like Signal.

# special topic: data brokers
You may be aware that Facebook, Google, and other companies ultimately sell data, but who exactly do they sell data too? More often than not, data is purchased by an entity known as a [data (information) broker](https://en.wikipedia.org/wiki/Information_broker). A data broker is essentially some entity that specializes in collecting data from various sources for a variety of reasons. Generally, the purpose may be to sell this data to another entity, however, one could consider people like private investigators data brokers as well. I won't go too deep into data brokers here, that can be for another post, but this should give you a general idea of what a data broker is.

Recently, there was a news story about a [Catholic Bishop](https://www.vice.com/en/article/pkbxp8/grindr-location-data-priest-weaponization-app) who was outed thanks to the location data that the app [Grindr](https://en.wikipedia.org/wiki/Grindr) collects and ultimately sells to data brokers. Grindr is a social media app that's primarily used by gay men, so you can see why this data would be of interest.

Imagine you're living in an area that's hostile to your sexual orientation. You use this app to try and meet others like you, discreetly, only to find out someone had purchased your location data and they were able to out you publicly. At best, you may become an outcast; at worst, you may be killed. 

A robust and adequate threat model may have prevented the bishop from being put into this position. Of course, being a member of the Catholic church and being homosexual has its own incompatibilities, but that's not really relevant here.

# disclaimer
The data broker topic has hopefully given you some cause to think that threat modeling is essential if you want to stay safe, but a quick disclaimer before we go too far.

Threat modeling is highly personal and unique to each individual. As such, there is no such thing as a "one size fits all" threat model. Someone who lives downtown in major city will have a widely different threat model than someone who lives in a rural town. Even two people living in the same city on the same block will have different threat models. 

Furthermore, you are never actually "done" threat modeling. There is no perfect threat model that you can design today that will applicable to tomorrow. Threat models grow and change with you, and it's okay if you don't get it completely right the first time as long as you continue to tweak it to your current needs.

Finally, threat models are intended to be a proactive approach to threats rather than a reactive approach. That is, a threat model doesn't necessarily do you much good if you've already lost the data that you're trying to protect. However, it can prevent further loss of information and allow you to course correct.

# what do you want to protect?
The first step is to take stock of your digital life to determine what's important to you. These days, almost everything we do is digital in one way, shape, or form, so this can be very difficult to do. For example, you likely have an email address, chat app, text (SMS) messages, and so on.

If you have multiple accounts or sources of data, you should initially start with your main accounts and then worry about the alts (alternates) later. The main ones will be the one you'll likely need to lock down more; however, you will also need to take care with your alts. Keep in mind that the threat model for your main account may different from your alt account. For example, your alt account becoming compromised may not lead to a catastrophic event especially if the purpose of that account was just to be thrown away anyway after making a single shitpost on reddit.

# determining your threat actors
After listing out the information you wish to protect, you can begin to list out your possible threat actors. A threat actor is simply someone or something that can act on a given threat. For example, if you're hunted by the federal government, the FBI would be a threat actor.

The threat actors you list out can be very broad or very specific. It really depends on who or what you're attempting to hide from. For example: local law enforcement, corporations, crazy exes, and/or an asshole coworker. While I had suggested being broad, it's also important to be specific which sounds contradictory. One example of this is hiding from local law enforcement vs. federal law enforcement. It's one thing to hide from local cops, but another completely to hide from the FBI.

# determining your threats 
The threat actors that you identify will begin to shape the threats that they pose to you and the likelihood that entity can act on a particular threat. Here, you'll need to combine your threat actors with the information you'd like to protect to generate the threats that are posed by said actors.

For example, you want to protect your text message communication from local law enforcement. If they have probable cause, they then likely have the ability to snoop on all SMS communication from your phone. Therefore, the threat you'd identify here would be something like "interception of messages", and you'd assign that threat to your SMS messages.

You shouldn't assign threats to threat actors, but rather assign threats to what you're protecting. Knowing the threat actor is more to help you identify what threats are within the realm of possibility.

Let's say you have an alt Twitter account because you don't want to be horny on main. Furthermore, your desires are... unconventional. It would be bad if someone were able to link your main account with this alt account, therefore, [doxing](https://en.wikipedia.org/wiki/Doxing) would be a serious threat.

It's okay if you identify threats that cannot be attributed to a particular threat actor. Again, the threat actors are just to help you identify threats that could happen, but identifying a threat without an actor would put you ahead of an actor you didn't think of at the time. 

# going further: determining risk levels
Risk management is beyond the scope of this post, but it's worth mentioning. Basically, for each threat, you should determine its likelihood of happening as well as the impact it can cause should it happen. Where these two intersect is your risk level for a given threat. If you know you're being targeted by a specific threat actor, you could include the threat actor in as well. For example, law enforcement being able to intercept your SMS communication is very likely and the impact would likely be critical; however, a jilted lover being able to do the same thing is rather unlikely, but again, should they succeed, the impact would likely be critical.

For example, you could use a 5x5 matrix that looks like the following (columns severity; rows are likelihood):

|               | negligible | low      | moderate | high     | critical |
|---------------|------------|----------|----------|----------|----------|
| very likely   | low        | moderate | high     | high     | high     |
| likely        | low        | moderate | moderate | high     | high     |
| possible      | low        | low      | moderate | moderate | high     |
| unlikely      | low        | low      | moderate | moderate | moderate |
| very unlikely | low        | low      | low      | moderate | moderate |

Note that you can use smaller matrices (e.g. 3x3), but you won't be able to be as detailed.

When performing your risk assessment, you should determine where you're at right now. Another risk assessment can be done after you've implemented mitigations which may reduce the likelihood and/or severity level. However, not all mitigations will reduce both, and not all will reduce both at the same rate. For example, even if you take precautions against doxing to make the likelihood unlikely, the severity could still be critical should you end up getting doxed. 

Risk management is helpful for determining where you need to put your resources. You don't want to waste time and resources on something that is a low risk if you have a high risk threat that needs to be addressed.

This step is optional; however, as you can see, it can give you greater insight into the threats you face.

# conclusion and further reading 
The intention of threat modeling is not to make you paranoid. On the contrary, it should give you more confidence in yourself that you can preempt your threats as they come up. However, threat modeling is hard, and its difficulty is compounded by the fact that the data we create is stolen in so many unique ways.

In a future post, I will outline the concepts and tools you can use to aid you in your pursuit of threat modeling and staying safe online.

For further reading, I recommend checking out the [EFF's Surveillance Self-defense guide](https://ssd.eff.org/). If you want to get extreme into threat modeling and privacy, I highly recommend [Extreme Privacy: What It Takes To Disappear](https://inteltechniques.com/book7.html). Note: Extreme Privacy isn't an exaggeration.



