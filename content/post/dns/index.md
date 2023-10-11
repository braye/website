---
title: "DNS"
date: 2022-12-15T10:11:34-08:00
draft: false
author: nepi
categories:
  - software
tags:
  - kubernetes
  - dns
  - education
---

<sup>*Note: This is a revised and expanded version of a [fediverse post](https://fedi.snepi.space/notice/AQbna9WkpTBcPsJG0O) I made the other day.*</sup>

I think we (as an industry) do a poor job teaching people about DNS.

DNS is often summarized as "a way to take words and translate them to an IP address". This isn't technically false, but it's an incredibly condensed explanation. It creates an imagined reality where DNS names have a 1:1 correlation with computers. This causes developers (and frankly, enterprise network folks who should probably know better) to perceive DNS records as rigid, immutable, and un-reusable.

This couldn't be further from the truth, of course - DNS has oodles of features (for better and for worse) that allow you to bend a single name to your will. CNAMEs, SRV records, you name it.. the list goes on and on. These aren't covered in the elevator-pitch version of DNS, because they're immensely complicated, and you don't need to know the details in order to reason about the role DNS plays in your program... **most of the time.**

A great example of where this perceived reality breaks down is Kubernetes. I've witnessed developers run head-first into a wall trying to wrap their head around using DNS as a service discovery mechanism. Using non-FQDNs when referencing services in the same namespace and the other "creative" ways in which Kubernetes uses DNS blows minds, but also creates a **lot** of frustration because it goes against everything developers think they know about DNS.

Even in a non-kubernetes environment, it causes problems. All too often I'm met with bewildering stares when I suggest that we simply replace the thing behind a given DNS name with something else, or that we can simply assign DNS names based on where a microservice lives, logically.

The real problems occur when developers take this faulty understanding of DNS and use it to implement horrible patterns. I'm currently facing down the prospect of deconstructing several hundred microservices' Dockerfiles, which take a build argument and place it into their FROM statement. All so "if the url changes", it's easy to fix. As if it's impossible to simply redirect the old DNS name to a new one.

What we need to convey is that the "name" you request from DNS has significance beyond serving as a human-readable alternative to an IP address. When you register a DNS name, you're not saying "the short-hand for this computer is X". Instead, you're saying "We offer a resource named X". DNS is the automated mechanism by which you can route users to the physical implementation of that service.

Ultimately, I get the feeling that this is a broader problem than just people mis-understanding DNS. I've seen some pretty horrible OOP disasters where people are duplicating and re-writing things that could have been completely avoided if they knew *anything* about setting up their classes properly.

Either way, a "Falsehoods programmers believe about DNS" would probably go a long way.

\- nepi