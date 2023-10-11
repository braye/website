---
title: "The RTX 3090 FE or: how I learned to stop worrying and love Tcase"
date: 2022-01-21T16:03:41-08:00
draft: false
author: nepi
categories:
  - hardware
  - graphics
tags:
  - 3090
  - nvidia
description: breaking down the poor thermal design of the 3090 FE
images:
  - 3090-chonk.jpg
---

_Note: This was originally split across three separate blog posts as more stream-of-consciousness type posts. I've condensed the original posts into one, with some minor revisions._

<img
  src="3090-chonk.jpg"
  alt="the absolute lad that is the rtx 3090 FE">

> This thing is as big as my 1st gen X1 Carbon!

It was January 2021: I was the proud new owner of an RTX 3090 "Founder's Edition" after playing the stock-checking "game" for close to four months. Installation went well, and I was impressed with the performance uplift. However, I was less than impressed by some of the thermals and noise I was seeing and hearing. Memory junction temperatures were north of 100 degrees celsius, and the fan was spinning up *very* hard to compensate. Not exactly the premium $1499 experience I expected.

In their lust for performance, Nvidia's engineers elected to use "GDDR6X"[^1] on RTX 30 series flagship cards, and a *lot* of it. 24 whole gigabytes, in fact, on my fully-populated RTX 3090. This presented an intresting challenge: G6X modules only come in 1GB (8Gb) increments, which means Nvidia had to squeeze 24 of these things onto the 3090's PCB. Limitations on board space suck, but the 3090 is pretty damn large as far as GPUs go. They had to put some modules on the reverse side of the PCB, but that isn't unheard of.

Normally this would be an interesting footnote in a PCB analysis, but the 24 memory chips are pulling somewhere in the neighborhood of 60-70 watts of power in total. That fact turns this from a curiosity into a Big Fucking Problem™. See, the 3090 "Founder's Edition" has a fatal design flaw - it's too powerful! No, seriously. The 3090 FE has a serious (albeit solvable) thermal management problem when it comes to memory, which Nvidia not only refuses to acknowledge, but actively gaslights their consumers about.

## Analysis

Let's take a look at how the card behaves in an "average" gaming build, running complete default setting with regards to clocks, power targets, and fan curves. I used 3DMark Port Royal's stress test mode to grab these results, and each run was after a cold boot where the computer had been completely unpowered for \~30 minutes beforehand. I ran the card through 5 loops of the stress test mode, and then took a screenshot of HWinfo64 to grab the highest recorded temperatures. [^2]

<img
  src="3090-hwinfo-stock.png"
  alt="HWinfo64 data for a stock 3090 showing poor memory temperatures">

> Notice the third column, third row: 102 degrees Celsius. This is the T<sub>j</sub> of the hottest memory chip on the board. 

This is where the aforementioned gaslighting starts: the only spec that Micron lists on their [data sheet](https://media-www.micron.com/-/media/client/global/documents/products/data-sheet/dram/gddr/gddr6/gddr6x_sgram_8gb_brief.pdf?rev=161547726f0b45239d3da37ef29b09bf) is T<sub>case</sub>. They state that this should not go above 95 degrees. They **don't** state how to measure T<sub>c</sub>, but maybe there's some standard everyone uses which I'm not privy to as a lowly software developer. I would hazard a guess that it involves thermal paste, kapton tape, and a k-type thermocouple, the latter two I don't posses and have no intention of buying at the moment.

Regardless, Nvidia[^3] naturally took this distinction and [ran with it](https://forums.developer.nvidia.com/t/request-gpu-memory-junction-temperature-via-nvidia-smi-or-nvml-api/168346/160). They claim that the memory junction temperatures visible in Windows are exposed via an "undocumented" API, (presumably) might be reporting incorrect values, and that since T<sub>j</sub> != T<sub>case</sub>, there is no issue with the RTX 3090. This quickly becomes an obvious lie when you consider that the fan speeds on the graphics card are tied directly to the "undocumented" memory temperature measurement, but more on that later.

Full disclosure - I don't have any formal education in electronics. I'm not an EE, I don't even have a college degree. Even with that lack of knowledge, I do know one thing about electronics: they aren't magic. They're manufactured components just like anything else, and the same principles apply. You can't run something close to redline for most of its life, and expect it to last. Thermal stress, under/over-voltage, etc.. every operating parameter of a component affects its expected lifespan.

Given that knowledge, my reasoning around this issue boils down to one thing:

#### I might be "buy a $1500 GPU" privileged, but I'm not "throw away a $1500 GPU" privileged.

I have to imagine most people using flagship GPUs fit into this category. Perhaps I'm projecting, and people like me aren't as big of a market as I think. Maybe it has no actual effect on Nvidia's bottom line. However, I do know that this GPU demand can't last forever. I have to wonder who Nvidia is planning on selling their consumer cards to once they finally succeed at annoying every nerd on the planet, and the crypto folks have moved onto Proof of Stake or ASICs.

Rant over. Sorry. In my defense, these feelings had been stewing since 2009 when I first tried installing Nvidia's proprietary drivers on Fedora as a Linux noob. You'll have to excuse a little animosity. Anyways, back to the problem at hand.

## Performance Tuning and Overclocking

When I first discovered the issue, I wasn't thrilled with the prospects of modifying my brand new graphics card on a hardware level. Thankfully, that's not the only way to change the operating parameters of a component - there's always software performance tuning, known more generically as "overclocking".

I've been playing with overclocking...basically since I built my first custom PC. At that time, I approached it (like some others, I would imagine) as a way to squeeze extra bits of performance out of the hardware that I could afford, out of necessity. I couldn't afford top-of-the-line GPUs, but I still wanted to play top-of-the-line games! More recently, I've been able to engage with it as more of an engineering exercise; A fun, tangible way to apply knowledge of electric power, integrated circuits, and the relationship between the two.

### A Quick Primer on Overclocking

In the Bad Old Days, GPUs (and CPUs) contained a processor that ran at a fixed frequency. Whenever the processor was receiving power, it was running at this frequency even if it was just doing no-ops. There's some platform-specific nuance here, but in essence the way that you would "overclock" the processor is by raising that fixed frequency by a fixed amount. For instance, you could take a processor running at 350MHz and run it at 400MHz, by applying a 50MHz offset. Let's call this "offset" overclocking.

Sometime around the GeForce 8000 era[^4], NVidia introduced the idea of "2D" and "3D" clocks. The intention was that the GPU would dynamically adjust its clock to a very low level (100-200MHz or so) when its 3D graphics capabilities weren't being utilized. This became increasingly necessary as clock speeds, die sizes, and power draw grew over time. As before, this allowed you to add an offset with the additional caveat that it would only apply to the "3D" clocks. Fundamentally, it was the same as offset overclocking.

The modern method by which we overclock NVidia cards was introduced in the GTX 600 series. Known as "GPU Boost" to NVidia's marketing material, this algorithm takes thermals, power consumption, and stability of the core into consideration and dynamically selects a "boost clock" from a range of clock speeds. These "boost clocks" are greater than or equal to a set "base clock". Its initial iteration, however, overclocked much the same as the "old" methods. An offset was applied to the boost clock range, which in effect just shifted the range of selection up by a set amount of MHz.

With the release of the GTX 10 series cards in 2016, "GPU Boost" reached a state that is more or less the same as it exists today. Called "GPU Boost 3.0", the system would opportunistically push the limits of the GPU as long as certain conditions were met. One *very important* change was made, though: Users now had the ability to define a custom relationship between the GPU core's voltage, and the core's frequency. In essence, you end up with a graph like this:  

<img
  src="msi-afterburner-vf-curve.png"
  alt="A screenshot of a voltage-frequency curve in MSI Afterburner">

> [Source](https://www.reddit.com/r/overclocking/comments/7yjiwn/does_voltage_curve_also_benefit_memory_clock_msi/) on Reddit's r/overclocking

The system still retains the thermal and power-related checks of "GPU Boost" 1.0/2.0. You can't push the combination of voltage and frequency any farther than the power limit allows, and the GPU will drop clocks if it starts getting too warm.[^5]

I had been messing with Voltage/Frequency curves on my previous 10-series GPU in the months leading up to my 30-series purchase, so this process was deeply familiar to me and was the first thing that sprung to mind when I recognized that I had a thermal management problem. If you can _increase_ power draw via V/F curves, after all, it stands to reason that you can __lower__ it! The enthusiast community is very well aware of this - "undervolting" was popular with the past couple generations of NVidia GPUs, and even with AMD Ryzen CPUs which implement a similar opportunistic boosting algorithm. 

I got to work - after a couple hours in MSI Afterburner and 3DMark, I had managed to shave close to 60 Watts off the 3090's TDP while only dropping \~100MHz from the typical "boost" clock. The memory temperatures weren't great, but they were staying south of 100C and the card's fans were no longer spinning up to an unreasonable volume in an effort to mitigate the effects of NVidia's poor thermal design. Story over, right?

### Wrong.

This is where Nvidia's treatment of Linux users rears its ugly head. You _can not_ manage V/F curves using their proprietary driver on a Linux system.

Zero voltage control, actually. Not so much as an offset.

Naturally, when I decided to switch my main PC to Fedora a few weeks ago, this became a very apparent (and audible!) problem. A problem which I had actually completely forgotten about, because the solution just works __that well__ on Windows. This was, hilariously, the first thing that actually made me consider switching back to Windows. Time is a flat circle, after all, and it seems that we've come around to the era where _hardware support_ is the thing stopping people from switching to Linux!

By this point it had been close to a year since the initial purchase of the card. The warranty was ticking down, and any pleading e-mails in response to a card failure would probably be ghosted.

If software was off the table...maybe it was worth pursuing the hardware angle!

## Cooler Design and Thermal Management

Let's talk about the hardware design of the RTX 3090 "Founder's Edition", and GPU cooler design in general.

Until recently, Nvidia's first-party cards sported a "blower" design, using a paddle-wheel style fan to push air across a fin stack and exhaust it out the rear of the case. This seems to have debuted with the release of the 8800 series in 2006, and remained as their chosen thermal solution for over a decade. It was never particularly good, to be honest, and by even the early 2010s this design was beginning to show its weaknesses in the face of climbing TDPs[^6]. 

The release of the RTX 2000 series in 2018 showed Nvidia's first attempts to move on from this tired design. The thermal solution included on "Founders Edition" cards, as they were now branded, weren't outstanding but still represented a large upgrade over the old design. They were dual-fan, transverse-exit cards which utilized a large vapor chamber to spread heat across the heatsink. Two fans would then push air down onto the heatsink, and it would then exit the open sides of the card into the computer case. Large vapor chambers aren't the most efficient method of moving heat over a long distance, but they strike a good compromise between low cost and thermal performance. AMD recently used a similar design on their first-party Radeon 6800 series cards to fairly decent effect.

<img
  src="2080-fe-vapor-chamber.jpg"
  alt="A GeForce RTX 2080 Founder's Edition GPU, without its fan shroud">
  
> An RTX 2080 FE, minus the fan shroud.

The RTX 3000 series, on the other hand, debuted with a radical departure from the previous 20 series design. Nvidia's election to push TDPs even higher, and the limited space in which to dissipate that heat, led them to create a frankenstein-like marriage of the old "blower" concept and a more recent aftermarket trend known as "blow-through" coolers. Maybe it's for the best if I let Nvidia's marketing material explain:

<img
  src="rtx-30-series-flow.jpg"
  alt="A GeForce RTX 2080 Founder's Edition GPU, without its fan shroud">
  
The case-forward half of the card has a fan that's embedded on the "top" side of the heatsink, and spins to pull air *through* the heatpipes and fins below it. The case-rearward part of the card has a fan on the "bottom" of the heatsink, sucking up air and expelling it out the rear of the case through a stack of fins.
 
This design is... certainly unique, and its aesthetic fits Nvidia's almost Apple-like direction. For extracting heat from the GPU core, the there is a (presumably) copper coldplate mounted to the die, connected to several heat-pipes, which in turn have a fin structure attached to them. This has been popular with third-party card retailers for years, and is proven to be a high-performing, albeit more expensive option when it comes to heat dissipation. The rest of the components on the front side of the board (power ICs, RAM ICs) sink into a vapor chamber, which is seemingly attached to a couple flat heat-pipes which attach to the bottom of the fin-stack at the case-rear of the card.

I'm not exactly convinced that this is better than what's available from other manufacturers such as ASUS, EVGA, and Gigabyte. Admittedly, I haven't even done a Fermi estimate of how much heatsink area is being actively cooled, but my eyes tell me that it doesn't look great. The entire middle of the card is a dead-zone, since the fans are at the extremities, and the benefit of convection on the "wing" fin stacks is limited as the tops are obstructed by the PCB and a solid metal vanity cover.

<sub>(It looks dead sexy, though.)</sub>

The awkward implementation of a "blow-through" design also necessitates putting all the heat-generating parts of the PCB very close to one another. Here's a picture of a 3090 FE PCB - notice the borderline comical level of component density.

<img
  src="3090-fe-pcb.jpg"
  alt="A GeForce RTX 3090 Founder's Edition PCB">

Keep in mind that all of those power stages, all those RAM ICs, the voltage management ICs... all of that together is the 350 Watt figure we're dealing with when we refer to the card's TDP. There's little doubt that the heatsink has the thermal *mass* to deal with a 350 Watt heat load[^7], but that's only one part of the equation. Much like electricity, every time a connection needs to be made between two materials, there is some thermal resistance. Thermal paste and pads help to fill in the physical gaps, but they're no substitute for having less thermal interfaces. In the RTX 3090's instance, most of the components on the front side of the interface go through 3 thermal interfaces:

- A thermal pad, connecting the component's case to a vapor chamber.
- A connection between the vapor chamber and a flat(?)[^8] heatpipe.
- Another connection between heatpipe and fin stack. (from what I can see, only one side of the heat pipe)

This probably doesn't seem like a lot, and it's not unheard of for cards to do most of this stuff. But, like lots of engineering, it's more than the sum of its parts. The end result is that the cooler lacks sufficient dissipating power to keep the temperatures of all components under control. This leads to a behavior where heat slowly soaks through the PCB (the layers are copper, after all!) and temperatures slowly increase to borderline out-of-spec levels.

The problem isn't the power target, or the specifics of the cooler design, or the amount of RAM, or the type of RAM... It's all of these components together. It's like an airplane crash - it's a chain reaction of "this would have been fine, but.." scenarios. Each part contributes more or less equally to the end result.

## Knowledge is Power

Now that we've made it this far, though, we've acquired something very important! [Knowledge](https://www.youtube.com/watch?v=0GIwTG8V-Ko). Now that we know which parts of the equation affect thermals, let's pick one and change it. 

First off, we can't lower the heat load via V/F underclocking, *because Nvidia*. We could lower the heat load via power limits, but that kind of defeats the point of using this ridiculous thing. It also (supposedly) has negative effects on frame pacing. We could always explore complete replacements for the thermal solution, like water-cooling, but custom loops are your best bet for GPU cooling and the cheapest systems run hundreds of dollars and hours upon hours of time. Great as a hobby - not as a pragmatic solution to this very specific problem.

The first real solution I explored was just sticking some heatsinks on the back of the GPU. Why not? It wouldn't even require digging my Torx drivers from wherever they reside in the depths of my toolbox. Of course, one quick look at my computer and those hopes were dashed:

<img
  src="3090-no-space.jpg"
  alt="A GeForce RTX 3090 Founder's Edition PCB">
  
Oops. Turns out I forgot that there was approximately half a millimeter between the back of my GPU, and the heatsink-fan assembly for my CPU. That's gonna be a no-go. It's worth noting that Many People On The Internet claim to have success with this method - although every time I found a photo from said person, they were running a closed-loop liquid cooler for their CPU. That's actually where my mind went next - it would have been a decent upgrade in cooling capacity for my CPU, but I wasn't exactly running into limitations there. Ultimately, it would have mostly been wasted money.

If I couldn't add more dissipation to the card via additional surface area, I thought, maybe I could improve the thermal interface to the surface area that already existed. Some research revealed that this - like undervolting - was already very common in the enthusiast community. People had reported lots of success, dropping memory T<sub>j</sub> temps by 10 degrees or more, putting them firmly in the "warm but tolerable" zone. This was the method I decided to pursue.

I ended up purchasing Gelid 2.0mm thermal pads. Dubious W/mK claims aside, they looked and felt like a bunch of barely-contained thermal paste, which I concluded was probably a good thing. Later that day I got bored on my lunch break, and decided to begin the operation.

Upon liberating my Torx drivers from their dusty prison, I was greeted with the maze of passives and ICs that I expected:

<img
  src="3090-teardown-1.jpg"
  alt="My GeForce RTX 3090 Founder's Edition PCB">
  
Obnoxiously enough, the backplate requires Torx *and* Phillips drivers to remove. If you're taking score, put another mark in the "Why, Nvidia, Why?" column.

Looking closer, though, I immediately noticed a problem: a couple of the thermal pads evidently weren't making full contact with the RAM ICs! See those blank spots where the stock thermal pads weren't touching? Here's a closer look:

<img
  src="3090-teardown-3.jpg"
  alt="My GeForce RTX 3090 Founder's Edition PCB">
  
I don't really think this made that big of a difference, over all. It certainly wasn't pleasing to find, though. Looking closer at the thermal pads, it certainly looks like they were applied by hand, and weirdly enough not in pre-cut groups?

<img
  src="3090-teardown-2.jpg"
  alt="My GeForce RTX 3090 Founder's Edition thermal pads on the backplate">
  
The section on top is pretty egregiously out of alignment, and it looks like they come in groups of two, but not always? The ones on the left are half-cut. I don't want to rag on what are likely horrifically underpaid factory workers too harshly, but you'd think that an assembly line running RTX 3090s would at least have pre-sized pads for the coolers. It's probably not worth the extra prep time and logistics, I guess.

There was also this weird thermal pad going to seemingly nowhere? I only noticed this after I started replacing some of the pads.

<img
  src="3090-teardown-4.jpg"
  alt="My GeForce RTX 3090 Founder's Edition PCB, showing a thermal pad contacting an unpopulated spot">

It looks like it was meant to make contact with some 8-pin IC which was removed from the final design. For reference, this card was purchased in January of 2021, so it was by no means a super early production run or pre-production sample.

The stock thermal pads are also significantly different than the ones I purchased after-market. They're this weird, almost fabric-like material.

<img
  src="3090-thermal-pad.jpg"
  alt="My GeForce RTX 3090 Founder's Edition's stock thermal pads">

I have to imagine this makes them significantly easier to handle. These really did feel like a fabric, rather than a loosely stuck-together pile of weird sludge like my Gelid pads did. Speaking of those pads, I quickly grabbed my scissors and got to work cutting out the appropriate sizes and placements:

<img
  src="3090-repad-finished.jpg"
  alt="My GeForce RTX 3090 Founder's Edition, with new thermal pads">
  
I elected to leave the rows of stock pads, meant to sink heat from the power delivery circuitry on the front side of the board, in place. Frankly, I doubt their effect is make-or-break on the thermals for those ICs. Power stages are generally good into the triple digits. I suppose I could look up the data sheet if I really wanted, but I don't plan on pushing the power on this card significantly beyond stock. Maybe I'll regret that in a couple years, who knows.

Overall, the installation was fairly un-eventful. The card went back together easily enough (it was just the backplate after all), and I slapped it back into my PC, ready to do another round of testing.

## Results

As a reminder, here are the pre-modification results:

<img
  src="3090-hwinfo-stock.png"
  alt="HWinfo64 data for a stock 3090 showing poor memory temperatures">

Not pretty. The mod, however, yields a different story. As a reminder, this was after 5 loops of 3DMark Port Royal's stress test mode, at completely stock settings.

<img
  src="3090-hwinfo-modified.png"
  alt="HWinfo64 data for a stock 3090 showing much improved memory temperatures">

### MUCH better!

The hottest memory T<sub>j</sub> dropped by 12 degrees! This was an awesome improvement, _much_ more than I expected from a simple thermal pad replacement. Most importantly, take a look at those GPU Fan entries - A 300 RPM drop in max fan speed throughout the same test. 300 RPM doesn't sound like a lot, but it's the difference between the card being a quiet "whoosh", and and annoying "whaaaaaa". Take a listen:

Pre-Mod:
<video
  controls="true"
  src="3090-pre-mod.mov">
  
Post-Mod:
<video
  controls="true"
  src="3090-post-mod.mov">
  
  
The card has *horrendous* coil whine, so it still sounds like a taser every time I run a heavy benchmark, but I can barely hear that noise once I have my headphones on.

I hope this article helps anyone who finds themselves in a similar situation. This *really* annoyed me, and fixing it was probably the most satisfying thing I've done in months. This area of PC building - thermal management - feels like it could use some love from the community; There aren't many good tools for managing things like fan profiles outside of the poorly-written Windows software that motherboard manufacturers ship or proprietary products like the Corsair Commander. Something to think about, I suppose.

 -snepi

---

[^1]: It's not JEDEC, so I'm quoting it. @ me about it.
[^2]: I suppose I could have run it through more loops, but after about 5 runs (10 minutes) the card appeared to be pretty close to steady state thermals. Plus I was anxious to get my results.
[^3]: By all indications this response is written by a driver developer at Nvidia. IMO it's completely fair to say "Nvidia" is saying this, it's not some customer service rep who is in over their head.
[^4]: I could be completely off-base on this timeframe; Please @ me if I got this wrong.
[^5]: The influence of thermals on the algorithm starts earlier than you might intuitively think: Some users notice a "free" 15-30MHz bump when they manage to get the die close to room-temperature using liquid cooling.
[^6]: Thermal Design Power, functionally a measure of how much heat must be dissipated from a component at 100% utilization.
[^7]: Try picking this thing up!
[^8]: It's hard to tell from images I can find online, and I'm using my example to write this entry at the moment.
