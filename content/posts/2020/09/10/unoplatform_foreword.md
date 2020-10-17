---
draft: false
date: 2020-09-10T16:17:58+02:00
title: "Uno Platform - Foreword"
description: "My thoughts about this challenge"
categories: ["Software Development"]
tags: ["Uno Platform", "UWP", "C#", ".Net", "Visual Studio", "Microsoft"]
series: [ "Uno Platform" ]
---

Dear readers.

In the near future I will try to share my experiences and solutions with you about my migration from the I-Synergy Framework and I-Synergy applications to the Uno Platform.

For the people who do not know Uno I recommend to take a look at their [website](https://platform.uno/) and a summary of their latest [conference](https://blog.mzikmund.com/2020/08/unoconf-2020/).

This library makes it possible to use a single codebase to convert a UWP application to an application that can run on Windows, IOS, MacOS, Android and WebAssembly with the same look and feel. 

At once, a true native multi-platform application is now at your fingertips for c# and XAML developers.
The development within this project has gained so much momentum in the last year that other multi-platform frameworks have been swept off the table.
And, in my opinion, rightly so.

![](https://s3.amazonaws.com/uno-website-assets/wp-content/uploads/2020/08/06113447/high-level-architecture-copy.png)

An additional advantage is that the Universal Windows Platform has also developed a greater support base for UI developers as a result of this development, which makes it more unlikely that this development platform will disappear or be phased out.</p>

Today I spoke to [Ian Bebbington](https://ian.bebbs.co.uk/) who was recommended to me by [Sasha Krsmanovic](https://www.linkedin.com/in/sashakrsmanovic) to see what is possible to convert my own products. 

We talked about everything for over 2 hours and I found this very constructive.
Above all, this has also given me more confidence to make a serious attempt to make this migration. 

The platform is now stable enough for production software but a lot of things will still need to be sorted out.

An additional effect of the rapid popularity of the platform and its developments have caused the documentation to lag behind, especially for the advanced business. A bit the victim of success.

That's why I want to document all my experiences and research on this blog.

Currently, my software consists of 2 solutions.
- [.Net Standard, .Net Core and UWP including](https://github.com/I-Synergy/I-Synergy.Framework)
  - Core
  - Mvvm
  - UI (UWP control library)
- [I-Synergy applications including](https://www.i-synergy.nl)
  - .Net Core backend service
  - Business Manager
  - Point of Sales
  - Display
  - some small tools

In total, these are about 45 projects, all of which need to be included in this process.

I also hope to find the time and support to keep a decent track of this for future developers and interested parties.