---
title: "Testing 123"
date: 2022-01-10T22:11:34-08:00

---
# Hi

This is a hugo site that I threw together in ten minutes. This is a test post on said hugo site that I threw together in ten minutes.

I'll probably use this website to write about linux and stuff. I find nowadays that writing things down tends to help me organize my thoughts. It's also giving me the warm fuzzy feelings inside when I recognize that I'm publishing content on the internet without the help of anyone but my ISP. This is old-school hosting, from my house on an old computer. No runtimes, compute platforms, etc. in the way. Just me and the computer.

Terry A Davis's analogy about the OS as a motorcycle comes to mind. What a crazy, gifted man who just couldn't get the help he needed.

# Hey, who the hell starts a blog in 2021 anyway?

Me. Oh, I also recently gave up on windows for the last time. My gaming PC:

- ASRock X570 Steel Legend
- Ryzen 7 5800X
- GeForce RTX 3090 FE
- 32GB G-Skill Trident Z RGB DDR4-3400 C16

Now runs Fedora 35. We'll see how long that lasts. I didn't want to just go with Manjaro, because frankly every time I end up using an Arch-based system it ends up as kind of a mess. Also, I wanted to use GNOME and I don't really think there's a better place than Fedora to use GNOME. It's come a long way since the early days of GNOME 3. There's some idiosyncrasies to get used to, like not having desktop icons or a system tray (why god why). However it's buttery smooth on modern hardware, looks great, and comes with an excellent set of software to boot. In fact, this post is being written in gedit! Single-tapping the super key to show a grid of windows is quickly becoming one of my favorite things to do. I'm pretty sure this is a direct copy from macOS but I don't mind at all. Good UX is good UX.

Of course, choosing Fedora immediately bit me in the ass as Electron seems to have some problem with the glibc version that Fedora is currently using (2.34? 2.35? idk). This means I have to run Discord with `--no-sandbox`, which is probably bad for fifteen different reasons, but the biggest reason is that it keeps segfaulting when I'm in a voice call. The flatpak version doesn't have this problem. I might switch back if a fix takes too long.

Gaming overall has been a pleasant experience, _so far_. Putting aside the unspoken death, taxes, and Nvidia drivers being complete shit on linux, FFXIV worked more or less out of the box on Lutris after fiddling with a couple version dropdowns to clear up a stuttering issue. Trackmania 2020 was even simpler. Things have come a long way from the old process of checking 15 boxes in winetricks, and praying to your deity of choice. Performance in FFXIV and TM2020 is near indistinguishable from native. I'm sure this can be chalked up to my complete-overkill-for-1440p setup, but DXVK is putting in *serious* work here. I have an immense amount of respect for that project.

The only real problems I ran into were with some Steam games, which admittedly were my fault. I tried to be clever: by mounting my NTFS drives into my linux machine, and importing my steam libraries on those drives, I wouldn't have to re-download anything! This, also, immediately bit me in the ass when I tried to launch The Forest, which wouldn't work until I re-downloaded it. Oops.

Is this the year of the linux desktop? Find out next time on `dragon-ball.gz`!

(you hate reading those dumb puns? try thinking them up!)
