---
title: "Bicycle Gearing"
date: 2023-03-02T16:51:54-08:00
draft: false
author: nepi
categories:
  - bicycles
  - spreadsheets
description: In which we throw gasoline onto the horsepower/torque debate.
images:
  - /images/bike-gearing/bike.jpg
---

<img
  src="/images/bike-gearing/bike.jpg"
  alt="A picture of my green, single-speed road bike resting against a couple small bushes in my driveway.">

For those who don't know, I really enjoy cycling. It's been really good to me, as a hobby - I stay fit, I get outside, and bikes themselves are a tinkerer's dream. Bikes have all the of the fun mechanical parts - bearings, gears, chains, and pulleys - while being relatively attainable and easy to deal with. I definitely consider myself a car enthusiast, but it's much more realistic for me to acquire a project *bike* than it is a project *car*.

I've got three bikes:
 - The '74 Raleigh single-speed conversion pictured above,
 - a 2021 Cannondale Topstone 4 (the subject of this piece),
 - and a 2019 Trek Marlin 5 (reserved for crashing at Saint Edward state park)

The Topstone was the first "road bike" I ever purchased. Yeah, I know it says "Gravel" on the tin, but "Gravel Bike" means about as much as the phrase "best practices". If you ask 4 different people, you'll get 4 different responses. For my Topstone, it basically means it's a road bike with a front fork and rear seatstays that can fit wider tires, and a mountain bike drivetrain.

<center><sub>I think these used to be called Cyclocross bikes. Don't quote me on it, though.</sub></center>

Yeah, "mountain bike drivetrain". My only other bike at the time I bought the Topstone had an awful Shimano Tourney drivetrain with three chainrings on the crank. I really wanted to get away from that, and single-chainring drivetrains have been en vogue in the mountain bike industry lately. I went with the Topstone 4 for this reason. I thought that it was a good compromise between the form factor of a road bike, and the ability of a XC mountain bike and..it is! The bike handles small roots and gravel no problem. You'd hope so, considering it has "gravel" in the description.

One of the concessions that Cannondale's engineers made when equipping this bike was the 1x10 mountain bike drivetrain. The drivetrain spouts impressive range - the rear cassette goes from 11 teeth up to 48(!) teeth at the high end. This range is useful off-road, since you're usually tackling higher grades and winding trails. The lack of a front derailleur (in theory) eliminates complexity and weight, and makes it easier to pick the correct ratio when traversing quickly changing terrain.

That's all well and good, but that terrain has only been present for a handful of sessions in the two seasons I've ridden the bike. The machine is still plenty capable on the road, but as I alluded to earlier, engineering is a game of concessions. The drivetrain may have a large range, but it has a low number of gears with which to provide that range. This leads to situations where it's not easy to find a comfortable gear near the top end of the range (the smallest cogs). Finding the right gear is an incredibly important part of riding long distances quickly. Spin your legs too fast and you'll waste energy, but if the pedals have too much resistance you'll tire your legs prematurely. It's a delicate balance, and the gear setup on my bike didn't seem to be able to hit that balance.

## Quantifying the Problem

Just because we *feel* like a problem exists, does not mean that it does exist. Let's do some math!

...For the three of you that stuck around after hearing "bicycle math", I'm sure you know about the [website](https://www.sheldonbrown.com/) of the late, great Sheldon Brown. If you don't, congratulations! You're one of today's 10,000. It's one of the great remnants of the old web, along with [Evan Doorbell's website.](http://www.evan-doorbell.com/)

I was looking around for a calculator to punch in some gear combinations, which (naturally) led me back to Sheldon's site. Browsing the site is a bit like going to the university library - sometimes you'll stumble into sections that you didn't even realize were there, and find the most amazing stuff. The article on [Gain Ratios](https://www.sheldonbrown.com/gain.html) was one of those sections.

I can't possibly do the original article justice, so here's an excerpt:

>This number is a pure ratio, the units cancel out. I call this a "gain ratio" (with thanks to Osman Isvan for suggesting this term.) What it means is that for every inch, or kilometer, or furlong the pedal travels in its orbit around the bottom bracket, the bicycle will travel 5.58 inches, or kilometers, or furlongs.

For a mental shortcut, we can think of the Gain Ratio as the amount of effort required to move the bike from a standstill. Higher gain ratio, more effort. This isn't wholly accurate, but it's a good mental model that's intuitive if you've ever ridden a bike before. The difference from computing the raw gear ratio is that the Gain Ratio takes crank arm length and tire size into account.

The site has a [calculator](https://www.sheldonbrown.com/gear-calc.html), but I found it finicky on modern Firefox. It's one of the parts of the old web that hasn't aged so gracefully. Naturally, I went and recreated it in a spreadsheet. Let's see what that looks like for the factory Topstone 4:

<img src="/images/bike-gearing/topstone-stock.png">

The numbers clearly reflect what I've been feeling on the bike: the jump from the 2nd-smallest to smallest cog is a 15% hike in ratio! In fact, the whole cassette is really bad. The smallest jump is 12.5%.

Let's think about this, theoretically. Say we're riding along, and the gradient of the road I'm on increases from 1% to 2%. The negative acceleration that this gradient increase imparts on the system (the rider and the bicycle) is constant, regardless of gearing, because the gravitational pull of Earth is constant.<sup>[citation needed]</sup>

This isn't really a big deal at lower gain ratios, since the ratio works both ways. The torque multiplier between your legs and the road is high at lower gain ratios, so the ability to accelerate the system at a given level of effort is higher. Conversely, at higher gain ratios, your ability to add torque to the system (and therefore accelerate it) is diminshed.

Makes sense, right?

This starts getting into the weeds of Horsepower vs Torque (America's favorite debate), and I don't really have the will to explore it in-depth. High gear-to-gear difference at high Gain Ratios: not good. You're going to have to take my word for it.

Well, actually, you don't have to take my word for it. Now that we have our calculator, we can start fiddling with the numbers. More importantly, we can go about plugging in the values of cassettes designed by people who spent more time thinking about this kind of thing than I *ever* will. Let's do that!

Here's the Shimano 105 11-30 tooth cassette off Cannondale's 2023 CAAD13 105, which has a 52/36 front chainring:

<img src="/images/bike-gearing/caad13.png">

The difference between the four smallest cogs are all under 9%. Significantly lower than the 15% of the stock Topstone!

For giggles, let's swap a couple-generations-old Ultegra 12-30 cassette onto our Topstone.

<img src="/images/bike-gearing/topstone-ultegra.png">

A pattern emerges, which represents a clear acknowledgement of the problem I've been experiencing. Honestly, just discovering this was really exciting! It's fun when you can validate mental models you've built, empirically. It's like debugging, but for bikes.

Building this tool was so *insanely* helpful during the process of choosing the parts for my Topstone's upgrade. Bike fitting might be an art, but gearing is a science and I want to help that science in any way that I can. I'm making my gearing calculator available on [Google Sheets](https://docs.google.com/spreadsheets/d/12OCbyuAB1etyZ_HC-R799gs1cmtQAhSf30H7KO-iWVY/edit?usp=sharing), and via an [ods download, here on the site.](/docs/bike_calculator.ods) Excel should be able to open ODS files, but if yours can't for some reason, you should download [LibreOffice.](https://www.libreoffice.org/)

That's it for now!

Thanks.

\- nepi