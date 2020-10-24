---
categories:
- Software Development
date: "2020-09-13T19:09:19+02:00"
description: First some history about I-Synergy
draft: false
series:
- Uno Platform
tags:
- Uno Platform
- UWP
- C#
- .Net
- Visual Studio
- Microsoft
- I-Synergy
title: Uno Platform - History
---

Hi folks! 

Welcome to the first post in the series to make a migration from UWP to the Uno Platform. Don't get me wrong, despite this being a real migration, the Windows head will still consist of most of my original UWP code.

But before we can really start the conversion, it's important to know how the projects are set up to understand the interaction between the components.

So before we start writing something like code, we're going to discuss a piece of theory and architecture.

# History
In 2004, I started a blueprint to create a software program suitable for SMEs to manage their financial and administrative reporting. I also wanted to link as much functionality and systems as possible in order to achieve a true synergy of information and data. The intention was to connect the ERP, Point of Sales software, hardware and the web shop and use and exchange data in this way.

![](/images/vb6.jpg)

Starting with Visual Basic 6 and Windows Forms at that time, there where no ready-made popular libraries on Nuget, let alone the .Net Framework. A lot of functionality had to be developed by yourself.

The application consisted of one executable project and a ActiveX dll library in which everything else was in it. 

When I see the code now, I still get the feeling of nostalgia but also the urge to forget this knowledge very quickly. 

Let's just say that this has been part of the start of my career as a software developer. </p>

![](/images/asp.png)

In the meantime I gained experience with the classic ASP (Active Server Pages) for the web service I needed for the web shop. There was no n-tier development implemented or other methodologies used that are now very common. What I do remember is that web developments couldn't please me compared to compiled programming languages. Perhaps because VBScript or JScript was mainly used for this purpose. PHP was therefore a real no-go for me at that time. 

Also, the software ran with an included Microsoft Access database which did not make it suitable for a client server system.

![](/images/net.png)
![](/images/silverlight.png)

When the .Net Framework came out, and especially with the advent of WPF and Silverlight I saw the advantages of markup language being applied in a language where the UI was pulled away from the code. Also the nature of the structured document format known as XAML, made it easy to create your layout as most developers were accustomed to do in HTML.

In contrast with Windows Forms, if you drop a control on a form, the IDE ensures that the designer code behind it is generated "magically". There is no simple and error prone way to do this by hand. With XAML, now you got the opportunity to design the UI separately from the "software" code which makes it possible for a professional design team to work on it independently.

A development principle that is also often used with XAML is also called MVVM (model-view-view model). The web version of this concept is called MVC. Because the viewmodel (controller)  knows nothing about the view, it can be developed almost autonomously, which gives another very big advantage: The code can be unit tested.


![](/images/mvvm.png)

There are several frameworks available depending on what you need. I started with [Prism](https://github.com/PrismLibrary/Prism) by [Brian Lagunas](https://twitter.com/brianlagunas), [Catel](https://github.com/Catel/Catel) by [Geert van Horrik](https://geertvanhorrik.com) and then [MVVMLight](http://www.mvvmlight.net/) by [Laurant Bugnion](https://twitter.com/LBugnion).

They all met the most basic functions I needed at the time, but I noticed especially after the migration of my applications to UWP, that these libraries lacked a number of features that forced me to put something together myself.

Another additional bigger problem was that my Visual Basic knowledge was virtually useless because most of the new features on UWP and Windows Phone and code samples were written in C#. I needed to translate everything to VB first to understand what was going on before I could continue, which was very counter-intuitive.

The migration of all my programs and code from VB to C# in 2013 allowed me to make the transition very quickly, and boy, I'm so glad I did. I don't know any better now.

VB code now even feels a bit like dead weight. Thank you [Nick Muller](https://www.linkedin.com/in/nphmuller/) for your help and guidance ;)

# Present
Unnoticed, my applications became very large and very complex and difficult to handle, which made the need to restructure and divide the code very important. And it had to be done as soon as possible.

This resulted in separation of the software into 2 vertical columns.

The components I reuse in all my different applications, but basically anyone could use: 
- the I-Synergy Framework libraries

and the application specific projects for the market and my customers:
- the actual [I-Synergy software](https://www.i-synergy.nl). 

Another big migration I did 3 years ago is the conversion of the WPF application to full UWP. In this process, I worked with Microsoft and various hardware vendors, to be able to run the applications on [Windows 10 IoT Core](https://www.youtube.com/watch?v=N-vQSzAdPw4) on form-factor machines and even Raspberry Pi.

Last summer I also put the framework libraries on [github](https://github.com/I-Synergy/I-Synergy.Framework) as an open source project for other enthusiasts or interested parties. Maybe it's now the right time to start sharing with the community.

This project consists of about 19 projects each with its own specific function.
- I-Synergy.Framework.Core
- I-Synergy.Framework.Mvvm
- I-Synergy.Framework.Mathematics
- I-Synergy.Framework.Financial
- I-Synergy.Framework.Geography
- I-Synergy.Framework.AspNetCore
- I-Synergy.Framework.AspNetCore.Authentication
- I-Synergy.Framework.EntityFramework
- I-Synergy.Framework.MessageBus
- I-Synergy.Framework.MessageBus.Azure
- I-Synergy.Framework.Storage
- I-Synergy.Framework.Storage.Azure
- I-Synergy.Framework.Payment
- I-Synergy.Framework.Payment.Mollie
- I-Synergy.Framework.UI

I think most names are self-explanatory and indicate what they're meant for. 

But for our migration, the next projects are the most important.

### I-Synergy.Framework.Core (.Net Standard)
This contains all the common functionalities that can be used by both the libraries themselves and    the applications. Think of code that makes life easy. Basic classes, converters, extensions, utilities, helpers etc.
### I-Synergy.Framework.Mvvm (.Net Standard)
All the basic viewmodels for views, windows, bladeviews and blades that you encounter in a modern application.
### I-Synergy.Framework.UI (UAP)
Custom controls, converters, behaviors, styles, themes, and UWP specific services.
These 3 projects (nuget packages) are used in each UWP project as a basis to generate a fully functioning UWP application with a basic layout that can be extended with application specific code and UI.

So whether it's the Business Manager or Point of Sales, the basics are these 3 libraries.

This also means that our migration to the Uno Platform will initially focus on these 3 projects. Once we have completed that, we will only then be able to move on to the rest of the applications.

In the [repository](https://github.com/I-Synergy/I-Synergy.Framework/tree/features/uno_plaform) I will also add a Uno Platform application that will serve as a sample how to use the framework and to immediately check that everything works as it should.

Sounds very exciting all.