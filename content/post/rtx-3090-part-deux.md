---
title: "RTX 3090: Part Deux"
date: 2022-01-23T20:24:52-08:00
draft: false
---

<img
  src="/images/nvidia-hq.jpg"
  alt="A screenshot of a voltage-frequency curve in MSI Afterburner">

> A photo, completely unrelated to the subject of this post.

In my last post about the RTX 3090 FE, we ended up a little... okay, very off-topic. I'm going to try and keep it a bit more project-focused this time around. In my defense, those feelings had been stewing since 2009 when I first tried installing Nvidia's proprietary drivers on Fedora[^1]. You'll have to excuse a little animosity.

## Where did we leave off?

I had just finished demonstrating that the stock, unmodified design of the RTX 3090 FE provides insufficient cooling for the VRAM ICs located on the back of the card's circuit board. It was about three weeks after purchasing the card when I first noticed the problem. Given that this was the most expensive single computer component I had ever touched, let alone purchased, I wasn't exactly inclined to rip apart the card and start probing at things that I didn't fully understand. That seemed, to me, like an excellent way to immediately throw away all the work I had put into obtaining the card in the first place.

If hardware modification was out of the question, that left only one avenue: software modification. I'd been playing with overclocking...basically since I built my first computer. At that time, I approached it (like some others, I would imagine) as a way to squeeze extra bits of performance out of the hardware that I could afford, out of necessity. I couldn't afford a lot of hardware back then. More recently, I've been able to engage with it as more of an engineering exercise; A fun, tangible way to apply knowledge of electric power, integrated circuits, and the relationship between the two.

## Overclocking

A quick primer on overclocking:

In the Bad Old Days, GPUs (and CPUs) contained a processor that ran at a fixed frequency. Whenever the processor was receiving power, it was running at this frequency even if it was just doing no-ops. There's some platform-specific nuance here, but in essence the way that you would "overclock" the processor is by raising that fixed frequency by a fixed amount. For instance, you could take a processor running at 350MHz and run it at 400MHz, by applying a 50MHz offset. Let's call this "offset" overclocking.

Sometime around the GeForce 8000 era[^2], NVidia introduced the idea of "2D" and "3D" clocks. The intention was that the GPU would dynamically adjust its clock to a very low level (100-200MHz or so) when its 3D graphics capabilities weren't being utilized. This became increasingly necessary as clock speeds, die sizes, and power draw grew over time. As before, this allowed you to add an offset with the additional caveat that it would only apply to the "3D" clocks. Fundamentally, it was the same as offset overclocking.

The modern method by which we overclock graphics cards was introduced in the GTX 600 series. Known as "GPU Boost" to NVidia's marketing material, this algorithm takes thermals, power consumption, and stability of the core into consideration and dynamically selects a "boost clock" from a range of clock speeds. These "boost clocks" are greater than or equal to a set "base clock". Its initial iteration, however, overclocked much the same as the "old" methods. An offset was applied to the boost clock range, which in effect just shifted the range of selection up by a set amount of MHz.

With the release of the GTX 1000 series cards in 2016, "GPU Boost" reached a state that is more or less the same as it exists today. Called "GPU Boost 3.0", the system would opportunistically push the limits of the GPU as long as certain conditions were met. One *very important* change was made, though: Users now had the ability to define a custom relationship between the GPU core's voltage, and the core's frequency. In essence, you end up with a graph like this:  

<img
  src="/images/msi-afterburner-vf-curve.png"
  alt="A screenshot of a voltage-frequency curve in MSI Afterburner">

> [Source](https://www.reddit.com/r/overclocking/comments/7yjiwn/does_voltage_curve_also_benefit_memory_clock_msi/) on Reddit's r/overclocking

The system still retains the thermal and power-related checks of "GPU Boost" 1.0/2.0. You can't push the combination of voltage and frequency any farther than the power limit allows, and the GPU will drop clocks if it starts getting too warm.[^3]

I had been messing with Voltage/Frequency curves on my previous GPU[^4] in the months leading up to my 30-series purchase, so this process was deeply familiar to me and was the first thing that sprung to mind when I recognized that I had a thermal management problem. If you can increase power draw via V/F curves, after all, it stands to reason that you can lower it! The enthusiast community is very well aware of this - "undervolting" was popular with the past couple generations of NVidia GPUs, and even with AMD Ryzen CPUs which implement a similarly opportunistic boosting algorithm. 

I got to work - after a couple hours in MSI Afterburner and 3DMark, I had managed to shave close to 60 Watts off the 3090's TDP while only dropping ~100MHz from the typical "boost" clock. The memory temperatures weren't great, but they were staying south of 100C and the card's fans were no longer spinning up to an unreasonable volume in an effort to mitigate NVidia's liability for a faulty design.[^5] Story over, right?

### Of course not. This is Nvidia we're talking about.

This is where Nvidia's garbage treatment of Linux users rears its ugly head. You _can not_ manage V/F curves using the proprietary driver on Linux.

Zero voltage control, actually. Not so much as an offset.

Naturally, when I decided to switch my main PC to Fedora a few weeks ago, this became a very apparent (and audible!) problem. A problem which I had actually completely forgotten about, because the solution just works __that well__ on Windows. This was, hilariously, the first thing that actually made me consider switching back to Windows. Time is a flat circle, after all, and it seems that we've come around to the era where _hardware support_ is the thing stopping people from switching to Linux!

However, by this point it had been close to a year since the initial purchase of the card. The warranty was ticking down, and any pleading e-mails in response to a card failure would be ghosted.

If software was off the table...it was time for hardware.

 -snepi

[^1]: My parents weren't exactly thrilled with me printing out all those wiki articles.
[^2]: I could be completely off-base on this timeframe; Please @ me if I got this wrong.
[^3]: The influence of thermals on the algorithm starts earlier than you might intuitively think: Some users notice a "free" 15-30MHz bump when they manage to get the die close to room-temperature using liquid cooling.
[^4]: An EVGA GTX 1070 Ti SC2, for those keeping score at home.
[^5]: Not that anyone from NVidia will read this, but I should probably add that this is an opinion, for legal purposes. This entire website is an opinion, it's my blog!
