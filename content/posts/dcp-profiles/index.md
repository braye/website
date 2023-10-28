---
title: Fixing color issues with Lumix G7 RAW files
date: 2023-10-28T12:16:15-07:00
draft: false
toc: false
description: DCP Profiles are important!
---

I recently purchased a Panasonic Lumix G7. I wanted a more modern camera with good lens support; The only other interchangeable-lens camera I own is an old Sony a230 DSLR. It still works fine and takes good pictures, but Sony has completely abandoned DSLR cameras in favor of mirrorless cameras. I didn't want to be stuck without the ability to buy a new lens, if I broke one.

The G7, like all modern DSLR and mirrorless cameras, has the capability to store the images it captures as JPEG or "RAW" files. Most of the time, I find that I get decent results from the G7's JPEGs. However, I still have the camera configured to save every shot as both JPEG and RAW, on the off-chance that the camera really gets something wrong. 

For example, my Sony a230 is really bad about this - the JPEGs it produces are so blown-out and contrast-y that I don't even bother. The RAW files just look better. Well, imagine my surprise when I start shooting RAW with my brand new camera, and the images look like *shit*!

Here's an example that really showcases the issue. The first image is the JPEG from the camera, and the second is the RAW file processed by [RawTherapee](http://rawtherapee.com/).

![](organizer.jpg)

![](organizer_raw.jpg)

Those colors are *fucked!* RAW files are rarely "better" than JPEGs - they're usually just different in exposure, contrast, white balance, etc. But this is far from anything I'd seen before. It wasn't just RawTherapee having this problem either, I tried Darktable and LightZone with little success. I even sent a file to my friend who uses Adobe products, and we both agreed that something was wrong.

Thankfully, RawTherapee has a really good [wiki](https://rawpedia.rawtherapee.com/Main_Page), and while trying to resolve my problem I found my way to the article for the [Color Management module](http://rawpedia.rawtherapee.com/Color_Management). In the input profile section, it explained that it was possible to acquire color input profiles for certain camera models through Adobe's DNG[^1] converter.

Adobe DNG converter runs really well under Wine, and it did include a DCP profile for my camera. I found it in `C:/ProgramData/Adobe/CameraRaw/CameraProfiles/Camera`. There was a subfolder for my camera model within.

I configured RawTherapee to apply that DCP profile to any RAW image from my camera, and...

![](organizer_fixed.jpg)

Much better! It's not *exactly* the same as the JPEG - there's some very slight yellow casting/desaturation(?) when you look at them side-by-side. However, the reason I was looking at the RAW versions originally is that I felt the camera-produced JPEGs were a little over-saturated, so I'm happy. This is eminently more color-correctable than the RAWs processed without the profile.

[^1]: DNG is short for Digital Negative, an open container format for RAW data. Some camera vendors use it natively in-camera, for producing files from sensor data. Panasonic doesn't. Actually, most don't. It's a mess.