---
draft: false
date: 2020-09-16T20:18:59+02:00
title: "Uno Platform - Part 1"
description: "Prerequisites for your development environment"
categories: ["Software Development"]
tags: ["Uno Platform", "UWP", "C#", ".Net", "Visual Studio", "Microsoft"]
series: ["Uno Platform"]
---
Hi folks,

In order to install or use the [Uno Platform](https://platform.uno/) at all, we first need to arrange a number of things in advance. These steps are necessary because otherwise you will end up with all strange and vague errors especially in the Android head because the SDK's were not properly installed or were even outdated.

# SDK's
So the first step is to make sure that there are some SDK's installed in Visual Studio

1. Open Visual Studio Installer for this purpose.

![](/images/2020-09-16.png)

2. Choose the version e.g. Visual Studio Enterprise 2019
3. Press the "Modify" button
4. Select the "Workloads" tab and check the following items
   - Asp.Net and web development
   - .Net Core cross platform development
   - Universal Windows Platform development
   - Mobile development with .Net
 5. Select the "Individual components" tab

 ![](/images/2020-09-16-1.png)

 6. Check if the following items are checked
    - [Xamarin](https://dotnet.microsoft.com/apps/xamarin)
    - Android SDK setup (API level 28)
    - Windows 10 SDK 10.0.16299.0
    - Windows 10 SDK 10.0.17763.0 (or later versions if desired.)
    
    
To check that the Android head is working, I recommend creating a test project "Android App (Xamarin)" project next and starting it.

![](/images/2020-09-16-2-1.png)
> It's likely that the first time you will be asked to create a first virtual device in Android Device Manager. Make sure you use the correct Android version to match the SDK you installed. I chose Android 9 (API 28).

When running this test application, android opens the emulator and this head will be installed on the emulator and the app should eventually be visible. 

This can sometimes take a while to see something depending on how fast the deployment is. With me, it got stuck the first time and I had to restart the deployment.

![](/images/2020-09-16-3.png)
>If we see the app then that means that the emulator works well for the Uno Platform.

Now let's move on to the next step.

# Extension
If you've closed Visual Studio, restart it to install the required extensions.

This extension is a project template responsible for creating the right solution layout with projects for every head. </p>

1. Click "Extensions" on the menu in Visual Studio, and then click "Manage Extensions"

![](/images/2020-09-16-5.png)

2. Type in the "Uno" search window

![](/images/2020-09-16-6.png)

3. Under "[Uno Platform Solution Templates](https://github.com/unoplatform/uno)", click "Download"

![](/images/2020-09-16-7.png)

4. To complete the installation, visual studio needs to be restarted. Close the screen and close Visual Studio.

![](/images/2020-09-16-8.png)

5. Press "Modify" in the VSIX Installer

![](/images/2020-09-16-10.png)

6. If the installation has been successful, you can close this screen.

![](/images/2020-09-16-12.png)

# Testing
To test if everything works, we're going to restart Visual Studio and make another test project for Uno.

1. Start Visual Studio
2. Select the "Create a new project" option

![](/images/2020-09-16-25.png)

3. The newly added templates are at the top, otherwise you need to search for "Uno"

![](/images/2020-09-16-14.png)

4. Select "Cross-Platform App (Uno Platform)" and click "Next"

![](/images/2020-09-16-16.png)

5. Adjust the storage location for your project if necessary, click "Create"

![](/images/2020-09-16-17.png)

This process takes a while because all nuget packages are downloaded, but eventually you have a solution with an Android, IOS, macOS, UWP and WASM head.

![](/images/2020-09-16-19.png)

To see if the apps work, we start each head separately. Since I don't have an Apple I won't be able to run the IOS and MacOS head. We'll see.</p>

# Result

![](/images/2020-09-16-20.png)
> Windows (UWP x64)

![](/images/2020-09-16-21.png)
> Android

![](/images/2020-09-16-22.png)
> WASM (.Net Core)

![](/images/2020-09-16-24.png)
> IOS and MacOS

As I expected, I can't run this version because I don't have a mac.

I've had a HyperV MacOS VM in the past but that's just a little too far for this example. Hopefully there will come a time when this is no longer necessary and we can just build IOS apps on a Windows machine.

All in all, we now have a working environment where we can finally create Uno Platform apps.

Till next time 😉