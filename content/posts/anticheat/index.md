---
title: "Anticheat is Bullshit Security"
date: 2024-02-22T23:22:34-08:00
draft: false
toc: false
---

This is a rant.

I hate anti-cheat software in video games. It is, in my eyes, the biggest example of "bullshit security" software out there. Modern snake-oil.

Metaphor!
---
Imagine this scenario:

You want to determine whether I took an exam without cheating.

To determine if I cheated on the exam, you're going to pay someone to stand over my shoulder to make sure I'm not cheating. Much like a professor would do to a student that suddenly sat out of place on exam day.

However, the complication is that I'm doing this hypothetical exam in my own house. I can post notes in sneaky places, hide notecards under the bathroom sink, etc. This is made even easier if I have the list of "common places students hide cheat cards" distributed to all professors.

Do you see the problem?

The whole idea is **insane!** Nobody would ever give an exam in a student's private residence and expect any level of integrity.

That's exactly what games using client-side anti-cheat are trying (and failing) to do.

It's fundamentally broken.
---
The basis of this entire category of software (anti-cheat) is inherently flawed. It is, quite literally, the same as doing client-side data validation in a web app. Imagine if we had asked web browser creators to lock down the inspector tool and obfuscate memory, instead of implementing server-side validation!

The existence of the software itself demonstrates a lack of understanding. The idea of the game software being "protected" while running on an end user's computer is completely incongruent with the idea of security and trust in computing, for reasons demonstrated above. No software that takes integrity seriously is doing validation of data on the client-side.

So, what?
---
Anti-cheat software originated in an era where the compute to do validation on inputs and dynamic information culling was needlessly excessive. So, I'll grant the smallest modicum of mercy for not being willing or able to shake up the status quo. However, I refuse to believe that we're not well past that point. It's time that games modernized.

Well, they don't need *me* to tell them that - the software clearly doesn't work worth a damn. One look at any discussion about a competitive game will tell you that the hottest of the hot topics is cheating. Xenophobic underpinnings aside, the constant calls to "region lock China" don't come from people who are satisfied with the state of cheating.

The sad part is that...as technology people, we know this. We *know* that security by obscurity doesn't work. We know that client-side validation doesn't work. However, that doesn't stop the folks at Riot from getting on their platform and telling a hundred million sweaty League of Legends gamers that their kernel-level rootkit is the future, and that *this time, this time* they finally have something that works.

Of course, it won't. Because they're still trying to administer the test in the student's home.