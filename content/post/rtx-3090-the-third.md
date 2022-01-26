---
title: "RTX 3090: the Third"
date: 2022-01-24T17:42:46-08:00
author: snepi
categories:
  - hardware
  - graphics
tags:
  - 3090
  - nvidia
---

## Time to dig in!

Let's get cracking on the hardware design of the RTX 3090 "Founder's Edition". 

For the past couple generations, Nvidia has started putting a lot of design work into the cards that they OEM. Until somewhat recently, their cards sported the bog-standard blower cooling designs we'd been dealing with since the 8800GT days. By 2018, that design was getting *very* long in the tooth. Their first departure from this, the RTX 20 series FE cards, weren't necessarily outstanding but they represented a large upgrade over the old design. They were dual-fan, transverse-exit cards which utilized a large vapor chamber to spread heat across the heatsink. Not the most efficient method, but one which strikes a good compromise between low cost and thermals. AMD also recently used a similar design on their first-party Radeon 6000 series cards to fairly decent effect.

<img
  src="/images/2080-fe-vapor-chamber.jpg"
  alt="A GeForce RTX 2080 Founder's Edition GPU, without its fan shroud">
  
> An RTX 2080 FE, minus the fan shroud.

The RTX 30 series, on the other hand, was a radical departure from the previous 20 series design. Faced with climbing TDPs[^1] and limited (physical) space to dissipate them, Nvidia decided to create a frankenstein-like marriage of the "blower" concept and a more recent aftermarket trend known as "blow-through" coolers. Maybe it's for the best if I let Nvidia's marketing material explain:

<img
  src="/images/rtx-30-series-flow.jpg"
  alt="A GeForce RTX 2080 Founder's Edition GPU, without its fan shroud">
  
The case-forward half of the card has a fan that's embedded on the "top" side of the heatsink, and spins to pull air *through* the heatpipes and fins below it. The case-rearward part of the card has a fan on the "bottom" of the heatsink, sucking up air and expelling it out the rear of the case through a stack of fins.
 
This design is... certainly unique, and it fits the Apple-like direction which Nvidia leadership seem determined to drag their company. For extracting heat from the GPU core, the heatsink uses a coldplate mounted to the die, which sinks into heat-pipes, which then have a fin structure attached to them. This has been popular with third-party card retailers for years, and is proven to be a high-performing, albeit more expensive option when it comes to heat dissipation. The rest of the components on the front side of the board (power ICs, RAM ICs) sink into a vapor chamber, which is seemingly attached to a couple flat heat-pipes which attach to the bottom of the fin-stack at the case-rear of the card.

I'm not exactly convinced that this is better than what's available from other manufacturers such as ASUS, EVGA, and Gigabyte. Admittedly, I haven't even done a Fermi estimate of how much heatsink area is being actively cooled, but my eyes tell me that it doesn't look great. The entire middle of the card is a dead-zone, since the fans are at the extremeties, and you don't even get the benefit of convection on the "wing" fin stacks as the tops are obstructed by the PCB and a solid metal vanity cover. 

The awkward implementation of a "blow-through" design also necessitates putting power delivery significantly closer to the other heat-generating parts of the PCB. Here's a picture of a 3090 FE PCB - notice the borderline comical level of component density.

<img
  src="/images/3090-fe-pcb.jpg"
  alt="A GeForce RTX 3090 Founder's Edition PCB">

Keep in mind that all of those power stages, all those RAM ICs, the voltage management ICs... all of that together is the 350 Watt figure we're dealing with when we refer to the card's TDP. There's little doubt that the heatsink has the thermal *mass* to deal with a 350 Watt heat load[^2], but that's only one part of the equation. Much like electricity, every time a connection needs to be made between two materials, there is some thermal resistance. Thermal paste and pads help to fill in the physical gaps, but they're no substitute for having less thermal interfaces. In the RTX 3090's instance, most of the components on the front side of the interface go through 3 thermal interfaces:

- A thermal pad, connecting the component's case to a vapor chamber.
- A connection between the vapor chamber and a flat(?)[^3] heatpipe.
- Another connection between heatpipe and fin stack. (from what I can see, only one side of the heat pipe)

This probably doesn't seem like a lot, and it's not unheard of for cards to do most of this stuff. But, like most engineering, it's more than the sum of its parts. The end result is that the cooler lacks sufficient dissipating power to keep the temperatures of all components under control. The problem isn't the power target, or the specifics of the cooler design, or the amount of RAM, or the type of RAM... It's all of these components together. It's like an airplane crash - it's a chain reaction of "this would have been fine, but.." scenarios. Each part contributes more or less equally to the end result.

## Knowledge is Power

Now that we've made it this far, though, we've acquired something very important! [Knowledge](https://www.youtube.com/watch?v=0GIwTG8V-Ko). Now that we know which parts of the equation affect thermals, let's pick one and change it. 

First off, we can't lower the heat load via V/F underclocking, *because Nvidia*. We could lower the heat load via power limits, but that kind of defeats the point of using this ridiculous thing. It also (supposedly) has negative effects on frame pacing. We could always explore complete replacements for the thermal solution, like water-cooling, but custom loops are your best bet for GPU cooling and the cheapest systems run hundreds of dollars and hours upon hours of time. Great as a hobby - not as a pragmatic solution to this very specific problem.

The first real solution I explored was just sticking some heatsinks on the back of the GPU. Why not? It wouldn't even require digging my Torx drivers from wherever they reside in the depths of my toolbox. Of course, one quick look at my computer and those hopes were dashed:

<img
  src="/images/3090-no-space.jpg"
  alt="A GeForce RTX 3090 Founder's Edition PCB">
  
Oops. Turns out I forgot that there was approximately half a millimeter between the back of my GPU, and the heatsink-fan assembly for my CPU. That's gonna be have to be marked down as a failure to launch. It's worth noting that Many People On The Internet claim to have success with this method - although every time I found a photo from said person, they were running a closed loop liquid cooler for their CPU. That's actually where my mind went next - it would have been a decent upgrade in cooling capacity for my CPU, but I wasn't exactly running into limitations there. Ultimately, it would have mostly been wasted money.

If I couldn't add more dissipation to the card via additional surface area, I thought, maybe I could improve the thermal interface. Some cursory research revealed that this - like undervolting - was already very common in the enthusiast community. People had reported lots of success, dropping memory T<sub>j</sub> temps by 10 degrees or more, putting them firmly in the "warm but tolerable" zone. This was the avenue I decided to head down.

## Getting Goopy

A couple days later, my thermal pads arrived. I ended up purchasing Gelid 2.0mm thermal pads. Dubious W/mK claims aside, they looked and felt like a bunch of barely-contained thermal paste, which I concluded was probably a good thing. Later that day I got bored on my lunch break, and decided to begin the operation.

Upon liberating my Torx drivers from their dusty prison, I was greeted with the maze of passives and ICs that I expected:

<img
  src="/images/3090-teardown-1.jpg"
  alt="My GeForce RTX 3090 Founder's Edition PCB">
  
Obnoxiously enough, the backplate requires Torx *and* Phillips drivers to remove. If you're taking score, put another mark in the "Why, Nvidia, Why?" column.

Looking closer, though, I immediately noticed a problem: a couple of the thermal pads evidently weren't making full contact with the RAM ICs! See those blank spots where the stock thermal pads weren't touching? Here's a closer look:

<img
  src="/images/3090-teardown-3.jpg"
  alt="My GeForce RTX 3090 Founder's Edition PCB">
  
I don't really think this made that big of a difference, over all. It certainly wasn't pleasing to find, though. Looking closer at the thermal pads, it certainly looks like they were applied by hand, and weirdly enough not in pre-cut groups?

<img
  src="/images/3090-teardown-2.jpg"
  alt="My GeForce RTX 3090 Founder's Edition thermal pads on the backplate">
  
The section on top is pretty egregiously out of alignment, and it looks like they come in groups of two, but not always? The ones on the left are half-cut. I mean, I don't want to rag on what are likely horrifically underpaid factory workers too harshly. You'd think that a line running RTX 3090s would at least have pre-sized pads for the coolers, though. It's probably not worth the extra prep time and logistics.

There was also this weird thermal pad going to seemingly nowhere? I only noticed this after I started replacing some of the pads.

<img
  src="/images/3090-teardown-4.jpg"
  alt="My GeForce RTX 3090 Founder's Edition PCB, showing a thermal pad contacting an unpopulated spot">

It looks like it was meant to make contact with some 8-pin IC which was removed from the final design. For reference, this card was purchased in January of 2021, so it was by no means a super early production run or pre-production sample.

The stock thermal pads are also significantly different than the ones I purchased after-market. They're this weird, almost fabric-like material.

<img
  src="/images/3090-thermal-pad.jpg"
  alt="My GeForce RTX 3090 Founder's Edition's stock thermal pads">

I have to imagine this makes them significantly easier to handle. These really did feel like a fabric, rather than a loosely stuck-together pile of weird goop like my Gelid pads did. Speaking of those pads, I quickly grabbed my scissors and got to work cutting out the appropriate sizes and placements:

<img
  src="/images/3090-repad-finished.jpg"
  alt="My GeForce RTX 3090 Founder's Edition, with new thermal pads">
  
I elected to leave the rows of stock pads, meant to sink heat from the power delivery circuitry on the front side of the board, in place. Frankly, I doubt their effect is make-or-break on the thermals for those ICs. Power stages are generally good into the triple digits. I suppose I could look up the data sheet if I really wanted, but I don't plan on pushing the power on this card significantly beyond stock. Maybe I'll regret that in a couple years, who knows.

## Results

Overall, the installation was fairly un-eventful. The card went back together easily enough (it was just the backplate after all), and I slapped it back into my PC, ready to do another round of testing.

As a reminder, here are the pre-modification results:

<img
  src="/images/3090-hwinfo-stock.png"
  alt="HWinfo64 data for a stock 3090 showing poor memory temperatures">

Not pretty. The mod, however, yields a different story. As a reminder, this was after 5 loops of 3DMark Port Royal's stress test mode, at completely stock settings.

<img
  src="/images/3090-hwinfo-modified.png"
  alt="HWinfo64 data for a stock 3090 showing much improved memory temperatures">

### MUCH better!

The hottest memory T<sub>j</sub> dropped by 12 degrees! This was an awesome improvement, _much_ more than I expected from a simple thermal pad replacement. Most importantly, take a look at those GPU Fan entries - A 300 RPM drop in max fan speed throughout the same test. 300 RPM doesn't sound like a lot, but it's the difference between the card being a quiet "whoosh", and and annoying "whaaaaaa". Take a listen:

Pre-Mod:
<video
  controls="true"
  src="/videos/3090-pre-mod.mov">
  
Post-Mod:
<video
  controls="true"
  src="/videos/3090-post-mod.mov">
  
  
The card has *horrendous* coil whine, so it still sounds like a taser every time I run a heavy benchmark, but I can barely hear that noise once I have my headphones on.

I hope this article helps someone out there in a similar situation. This *really* annoyed me, and fixing it was probably the most satisfying thing I've done in months. This area of PC building - thermal management - feels like it could use some love from the community; There aren't many good tools for managing things like fan profiles outside of the poorly-written Windows software that motherboard manufacturers ship or proprietary products like the Corsair Commander. Something to think about, I suppose.

 -snepi

---
Parts: [One](/post/2022/01/21/the-rtx-3090-fe-or-how-i-learned-to-stop-worrying-and-love-tcase/) | [Two](/post/2022/01/23/rtx-3090-part-deux/) | Three 

[^1]: Thermal Design Power, functionally a measure of how much heat must be dissipated from a component at 100% utilization.
[^2]: Try picking this thing up!
[^3]: It's hard to tell from images I can find online, and I'm using my example to write this entry at the moment.
