---
slug: wi-fi
title: "Wi-Fi Sucks"
date: 2022-03-01
draft: false
---

<img
  src="/images/wetlands.jpg"
  alt="A picture of some wetlands/a lake somewhere east of Seattle">

Last fall, I moved across the country to live in the Seattle area. It's been great so far! Sure, most of what I've seen has been the winter months, but it's a welcome change from snowy old Michigan. 

What wasn't so welcome, on the other hand, is that my new bedroom doesn't have easy access to an ethernet connection. As someone who is terminally online, this was bad news. The closest ethernet jack was in the next bedroom. Besides the fact that it's occupied by someone else, it meant that if I wanted a wired connection it would require running cable across doorways, under door jams, and just generally in places it shouldn't go.

Crap, I thought. I was going to be relegated to wifi! Even worse, my room was above the kitchen. [I was literally going to be the punchline of an XKCD!](https://xkcd.com/654/) Of course, that comic is a decade and a half old (let that one sink in) and things have come a long way in 802.11-land.

First, and directly related to the aforementioned comic, is that 5GHz 802.11 service is now relatively common in consumer equipment. Combine that with decades of progress in protocols and adapters, and you get a much brighter picture - in the right setup, 802.11ax has the theoretical potential to deliver speeds exceeding that of gigabit ethernet!

We've come a long way on the software side too - long gone are the days of ndiswrapper on Linux (look it up, yes the driver situation was that bad). Most chipsets work out of the box. Hell, even **Broadcom** stuff seems to have halfway decent support now, although I'm 100% certain it's an order of magnitude worse than comparable Intel and Atheros products.

OFDMA in particular is a huge leap forward in making the Wi-Fi experience a bit less distinguishable from a wired connection. Introduced with 802.11ax (the kids call it Wi-Fi 6), the general idea is that it's way less expensive (in terms of time) to send small packets over a Wi-Fi network. This was the reason that games traditionally tended to perform poorly over Wi-Fi: Games usually throw many small packets over the network very quickly, and expect returning packets at a comparable rate. Basically the worst case scenario is that the time cost of sending a packet jumps around, essentially introducing jitter to the connection.

That's about as far as I can go with OFDMA on this blog - I don't know nearly enough about signal theory and 802.11 to explain more in depth than that. Me personally, I'm going to file this in the "I know enough to make decisions, and the rest is RF black magic" drawer. I'll put it right next to antenna design.

# Hey, I thought you said Wi-Fi sucks!

It'll take a minute to get there, but I promise it's coming. Let's flash back real quick to when I first set up the Internet at the new house. I chose my weapons - in my case an Asus branded PCIe adapter sporting an Intel AX200 chipset, and a Netgear RAX43 router. I'd had good luck with Netgear's Nighthawk line - my R6400v2 is still kicking around, never skipped a beat, and had an AP mode built into the firmware (my real goal). I got home, set them up, and... they just worked.

It was kind of underwhelming, frankly - but there was so much else going on at the time that I was simply glad to have working internet to my main PC. I went into the firmware, maxed out the connection speeds, turned on OFDMA, and figured I was good to go.

That is, until a few months later when I made the switch to Fedora. To be fair, that's not quite accurate - I had about a month of relatively pain-free gaming on Fedora before I started noticing problems. 

The first place I noticed issues manifesting was on TeamSpeak, a VOIP program. I'd get what sounded like packet loss, maybe...twice a minute. I was willing to chalk this up to shoddy internet at first - it's practically a joke within our friend group when the TeamSpeak server starts acting up. Unfortunately that theory quickly was thrown to the wayside as it became clear that I was the only one experiencing the issue. It was something on *my* network. Ugh. At the time, I wrote this off; Simply a cost of moving to Linux, and such a minor inconvenience that it wasn't worth switching back.

# Straying off the path...

It's at this point I need to make a confession: I have a problem. That problem's name is Path of Exile. It's a horrible skinner-box, but it's *my* skinner-box. Let's take a quick look at a couple relevant things that make Path of Exile unique, as far as ARPGs go.

First, in Path of Exile, there's a mode known as Hardcore mode. It's a staple of the ARPG genre, introduced with Blizzard's Diablo II in 2000. In a nutshell, it's Matrix rules - if your character dies in the game, they *stay dead*. This changes the way people approach the game significantly, and it's spawned its own mini-subculture within the community.

It's also important to understand that PoE isn't just a single-player game, like most other ARPGs. I'd call it an MMO-lite: There are hub towns where you can see other players, a full market system, etc.. But you're not necessarily interacting with other people all the time. To facilitate this MMO-lite behavior, your game session is essentially always multi-player. On a technical level, a central server is informing your client of changes to the game world.

The intersection of these two facts - the existence of Hardcore mode, where a slip-up can wash dozens of hours of progress down the drain, and the fact that the game uses a client-server architecture at all times creates an interesting problem:

**How should the game handle latency and desync?**

Most games use some combination of interpolation and prediction to smooth over the effects of network latency. This leads to a game world that is "fake", but feels responsive and snappy, which is way better for enjoyment and game-feel. Hardcore PoE players, however, play at such a knife edge that the game feeding players bad information has *huge*, negative implications on player enjoyment. Nothing is worse than losing because the game **lied to you** about where something in the game world was located.

Ultimately, the developers came to the conclusion that it'd be better to trade input latency and responsiveness for the ability to display a more accurate representation of the game world to the user. They implemented what's called "lockstep" mode, which is similar to delay-based netcode in some fighting games. They worked around the input latency issue by provisioning world servers in geographically advantageous locations, close to their users, and only having them connect to other world servers when absolutely necessary (i.e. for mmo-lite interactions).

Most importantly, this behavior is the default. So, given my troubles with TeamSpeak, you can imagine what I witnessed when I launched PoE to play the new seasonal league.

Actually, you don't have to imagine. Here's a video:

<video
  controls="true"
  src="/videos/poe_lag.webm">

I think it speaks for itself - It's the video-game equivalent of nails on a chalkboard. It strikes some deep, evolutionary nerve that causes physical pain.


# ...And down the rabbit hole.

Now, before any PoE fans think I'm _completely_ clueless, I do recognize that you can switch back to the old, "predictive" netcode model. I tried it, and it fixed the problem. I can't help but feel that it kind of misses the point, though... I'm not the kind of person that turns up the radio when my car starts making a new noise. That radio gets turned off until I figure out what's going on! Something was still **seriously** wrong with my network, and not every game/application is going to be friendly to this problem. I _had_ to fix this.

The first place I looked for issues were Wi-Fi channels. I live in a pretty dense neighborhood, so it's possible that someone was stepping over me with a ton of traffic on the same channel. Unfortunately, that wasn't the case - I moved my router into the less-populated DFS channels shortly after moving in.

### What's DFS? 

It's an abbreviation for Dynamic Frequency Selection. In North America, it's a section of frequency spectrum that's been opened up for usage by Wi-Fi routers, with some strings attached. The portion of spectrum it opens up is used by C-band radar, most notably by weather radar services. This (obviously) causes some issues, so where the "Dynamic" bit comes in is if a wireless router detects radar activity in the band it's transmitting in, the router _must_ automatically switch the 5GHz radio to another frequency. This prevents interference that can cause weird artifacts to show up on radar images. **RF is fun, isn't it?**

So, it's not interference...maybe it's those fancy 160MHz channels? I'm no stranger to paying early-adopter tax, and 802.11ax routers that do 2x2 160MHz channels aren't exactly overflowing in the bargain bin at Walmart. What if I tried switching to 80MHz channels instead? That worked!! ...for a time. It took a couple days, but eventually the same problem resurfaced.

At this point, I began to doubt my sanity. Was it a Wine bug? A kernel bug? Jesus, what if it was an adapter firmware bug? 

I was at a loss, so I began to think about the way the game was communicating over the network. As I mentioned earlier, games generally pass tons of small UDP packets over the network, and expect a reply in a reasonable amount of time. In a game with no compensation, like PoE, it's basically a tuning fork for the network. You're going to very distinctly feel any slight disruption. That's all well and good, but "the network" includes traversing the internet, as far as the game's concerned. How can I take the internet out of the equation?

I did a little searching, and couldn't find much in the way of diagnostic tools for this kind of problem. Not that I looked too closely, but most of this stuff tends to be proprietary and aimed towards businesses. Not the kind of thing I'm going to shell out for just to solve a silly little Wi-Fi problem. So I just kinda... wrote my own. [I called it bounceback.](https://github.com/braye/bounceback)

This tool acts like a game, throwing UDP packets across the network relatively quickly, and expecting a reply in turn. Instead of using it to push pretty pixels on a screen, however, it simply measures the time it took to get a reply and generates some useful statistics, which should hopefully let us find some kind of correlation.

Let's run it, and see what happens!

<img
  src="/images/bounceback-1.png"
  alt="The bounceback tool running on Linux, showing lots of jitter">

## :tada: I'm not crazy!!! :tada:

There **is** a problem on my local network! Even more interesting is how often it was occurring. I already had a vague sense that it had a pattern, but my tool confirmed it: every 30 seconds, almost on the dot.

Awesome, we _know_ the problem is occurring, and now we can measure it. There's no clear cause, but let's start with eliminating some pieces of the stack. Thankfully, I wrote the tool in Go, so it's easy enough to bring it over to my Windows disk (on the same machine) and do some testing there.

<img
  src="/images/bounceback-2.png"
  alt="The bounceback tool running on Linux, showing lots of jitter">

Well it's still happening, which is "good", I guess? It looks different though... there's a *lot* more packet disruptions, but they're significantly shorter. On the order of 20-30 milliseconds, rather than 100+. This probably explains why I wasn't having any issues with TeamSpeak on Windows - the interruptions weren't long enough to affect it. TeamSpeak's tolerance must lie somewhere between 40 and 100ms.

Okay, so we can cross Operating System off the list of culprits. Let's try switching the access point! I knew my old Nighthawk R6400v2 was lurking around, somewhere. I managed to dig it up, and set it up about 10 feet away from my computer, so signal strength wouldn't be the limiting factor here either. I threw the router on a DFS channel, started the bounceback server on my laptop, and ran the bounceback client on my desktop to find... the exact same result. Every 30 seconds, on the dot. In a fit of desperation, I even flashed DD-WRT to the R6400, only to continue experiencing the issue.

Okay, so it's not:

- Operating System
- AP Hardware
- AP Software

What else could it be? The only thing left was the adapter firmware...right?

Let's eliminate the firmware from the equation! The state of Wi-Fi is such that Wi-Fi expansion cards for desktops are literally just breakout boards for [M.2 connectors](https://en.wikipedia.org/wiki/M.2). This is great since it means that any Wi-Fi module that fits that form factor should "Just Work", and since every modern laptop has an M.2 Wi-Fi module, they're easy to come by.

I cracked open my Chromebook and...

<img
  src="/images/wifi-oops.jpg"
  alt="A mini-coax connector for laptop Wi-Fi, missing the connector">
  
Oops. 

Pro tip: don't use pliers on these types of coax connector. I was trying to find a better way than prying them with a plastic spudger, because I felt that put too much strain on the cable. Considering these results, though, I think that might be the intended way to disconnect these after all.

Resigning myself to fixing that later, I popped the card into my Wi-Fi breakout board and... the problem was still occuring!

At this point, I had a really strong feeling that I was missing some piece of crucial information. I got stuck for a few days. I swapped the cards back and tried playing around with power saving settings via `iw` commands, and it _sometimes_ fixed the issue. Definitely had an effect, but not the cause. It had to be something inherent to Wi-Fi...but *what*?

My breakthrough was _complete_ happenstance. I was running bounceback in the background while testing the aforementioned weird behavior with power saving. If I connected to an AP with power saving off, the behavior didn't happen, and I was trying to figure out why. I used GNOME to pull up the list of Wi-Fi APs for one reason or another...when suddenly I see bounceback going positively **wild!** The problem also manifested differently - it was *continuous*. The exact same latency spikes, but constant. **It must be happening when the system searches for APs!**

# Oh AP, Where Art Thou?

This explained everything - Why the problem occured on both Windows and Linux, why it followed me to different APs and AP firmwares.. Everything. Some cursory research on the internet yielded a couple [excellent](http://blog.cerowrt.org/post/disabling_channel_scans/) [blog posts](https://blogs.gnome.org/dcbw/2016/05/16/networkmanager-and-wifi-scans/) describing the situation. This was apparently such a pain point in NetworkManager that there was an actively maintained fork which disabled the periodic scanning behavior. 

As mentioned in the second linked blog post, one way to disable this scanning behavior is to pin the BSSID of the access point your computer is connecting to. Normally I'm against these kind of stop-gap solutions, but I was having a bear of a time reproducing my issue. Eventually, I caved and pinned the BSSID. I'm at least happy to report that it's been working fine over the past couple weeks.

If the point of this post was to let you know about channel scans, however, I could have done that in a couple paragraphs. What's important here is that this issue **consumed a week of my life**! Is that my fault? Partially. I'm not exactly known to be reasonable when it comes to stuff like this, but my insane behavior speaks to the bigger usability problem that Wi-Fi has. 

Throughout this whole process, I *constantly* felt like I was in the dark with regards to what my hardware was doing. I had to resort to swapping hardware components, relying on blind trial-and-error to resolve my problem. That's *never* a place you want to be, especially with expensive equipment like an 802.11ax router!

This probably stems from Wi-Fi traditionally being used in applications where people really don't care about performance, generally speaking. Video streaming, web browsing... those kind of things only require an internet connection that *works*, not one which is stable or particularly performant. My rough guess-timate is that of all Wi-Fi connections, 99% of them are from portable devices like smartphones, laptops, etc. As Dan's post mentioned, channel scans provide a utility for these - it's possible that a device leaves the range of one AP, and needs to find a new network or AP to connect to. Desktops, however, don't benefit at all, and it seems that 99% of Wi-Fi functionality is geared towards portable devices.

In addition to this, the tools to troubleshoot Wi-Fi behavior seem to start at "what's my signal strength" and immediately jump to "802.11 driver developer tools". Wavemon is the closest I got to a tool that gives me actionable information. It at the very least gives me a dBm number for signal strength along with encoding types, channel width, frequencies, etc. There's just not a lot of tooling out there, because it "Just Works" for 99% of use-cases.

Wi-Fi for desktops is still a pretty immature space, and for this reason I've been considering developing bounceback into a more general-purpose network troubleshooting/monitoring tool. It could run some basic checks, and point you in the right direction with regards to troubleshooting performance issues. Bandwidth testing would be a pretty logical first step.

Unfortunately, at the end of this the only suggestion I can make is to **stay away from Wi-Fi**. Unless you're in a situation like mine, don't be afraid of running some cable through the floor. I guarantee you'll spend less time with that than you will troubleshooting your Wi-Fi issues.

 -snepi
 
p.s. :flag_ua:
