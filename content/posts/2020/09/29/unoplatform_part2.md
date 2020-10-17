---
draft: false
date: 2020-09-29T20:18:59+02:00
title: "Uno Platform - Part 2"
description: "Adding samples before starting migration"
categories: ["Development"]
series: ["Uno Platform"]
---
Welcome to the next post in the blog series of converting my existing projects to the Uno Platform.

The part I'm going to start with is the I-Synergy Framework library.\
Before we start converting the library, we need to take care of a number of things that will ensure that the migration runs smoothly.

An important step IMHO is the creation of a test project where you can test the functionality or controls in a kind of sample project. This project should result in a workable application that shows existing functionality.\
This also allows us to see what bottlenecks or problems occur in our library.

Since I have tried this exercise before, I know that there are a set of problems you can avoid before even starting.

- Hyphen:\
If your project uses a hyphen in the name, namespace or in the folder, then you may run into problems where the whole project structure is no longer correct.\
By default, Visual studio replaces the hyphen with an underscore in the namespace. So for example , **"I-Synergy"** becomes **"I_Synergy"**. Because that underscore looks pretty ugly, I've always changed it to **"ISynergy"** by hand. But because the folders are still using the hyphen, when creating the Uno Platform project in Visual Studio,  you get all strange effects or errors in the folder structure that consists of combinations of underscores and hyphens.\
As a result, the project can no longer be built. You could change the files file by file, but I've noticed that the Android head in particular, doesn't handle this issue very well. Apparently Android doesn't want underscores or hyphens in the folder name and gives some obscure errors. There is another strange error that occurs that discourages the use of hyphens and underscores, namely:

- Path length:\
The use of hyphens and underscores make the folder paths unnecessarily long. There is a maximum path length of the folders, which creates an error when retrieving certain files. There is an issue open to [GitHub](https://github.com/microsoft/Windows-appsample-customers-orders-database/issues/19) which refers to this problem. It seems that a path should not be longer than 128 characters. The stupid thing is that you get an error message that a particular resource **".resw"** has not been found, but does not indicate that the path is simply too long.\
Hopefully, this will be resolved once by either removing the path limit or giving the correct error message. The shortest path as possible solves this problem.\
In my regular UWP applications I haven't encountered this problem before.\
So that in itself is strange. This issue has made me change all project names and folders from "I-Synergy.Framework…" to "ISynergy.Framework…"

# Creating the sample project
I recommend you to make a fork of the repository on [GitHub](https://github.com/I-Synergy/I-Synergy.Framework/tree/features/uno_plaform) to perform the same steps. This also gives you a better understanding of what is happening.\
But of course you can do this also with your own project.

![](/images/2020-09-27-6.png)
> If necessary, create a subfolder in your solution where you want this project.<br>I named this folder I-Synergy.Framework.UI under the Samples folder.

![](/images/2020-09-27-7-1.png)
> Right mouse click the folder and add a new project.

![](/images/2020-09-27-1.png)
> Select "Cross-Platform App (Uno Platform)" and then on "Next"

![](/images/2020-09-27-4.png)
> First select the right directory location where you want to save this project.\
Enter the correct "Project name" (e.g. ISynergy.Framework.UI.Sample) and click on "Create"

![](/images/2020-09-27-5.png)
> Windows Firewall can give you a notification about the Xamarin Messaging Broker that demands access.\
You can allow this one.

![](/images/2020-09-27-9.png)
> You can now see that a number of projects have been created within the sample project.

![](/images/2020-09-27-10.png)
> Because we don't have any Apple hardware available at this moment, the IOS and MacOS projects are useless so we remove them both.

![](/images/2020-09-27-11.png)
> Don't forget to remove the folders also. If needed, we can always add these projects later.\
This is to avoid eventual automatic build exceptions or just for sake of space.

![](/images/2020-09-27-12.png)
> What we have left are the UWP, Android and WebAssembly head projects and a separate shared library project where all the shared code will be placed for the sample project.

![](/images/2020-09-27-14.png)

Now we're going to check all packages for updates.
- In the framework use in the [Microsoft Extensions Logging nuget packages](https://www.nuget.org/packages?q=microsoft+extensions+logging). These are now in conflict because the Uno Project uses a lower version (1.1.1) of [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/3.1.8) and a non maintained version (1.1.2) of [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter). We can no longer use these so we remove them.
- We then update all Uno nuget packages to the latest available versions.</li><li>We're also updating the [Microsoft.NetCore.UniversakWindowsPlatform](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/) to the latest version (v6.2.10)
- If we have everything up to date then the list of updates is empty.

# Build new sample projects
The next step is to see if the project is still building. For this we first start building the UWP project.

![](/images/2020-09-27-19.png)
> Boom! 3 Errors!

I got 3 Errors, from which the first one seems the most logical.</p>
- "ILoggerFactory" does not contain a definition for "WithFilter"\
For this, I deleted the `ConfigureFilter` procedure in **App.xaml.cs** because I'm not going to use it.\
Also, remove the reference to it in the **App.xaml.cs** constructor

![](/images/2020-09-27-20.png)

After rebuilding this project, the other 2 exceptions where gone too.

Building the WebAssembly project went well except the warnings of\
`Windows.ApplicationModel.Activation.LaunchActivatedEventArgs.PreviousExecutionState`\
and `PrelaunchActivated` is not implemented in Uno.

Now the Android project…\
Again the same warnings as with the Wasm project but still successfully completed.

Everything seems to be going well now.

# Align the UWP version with the existing library
Next step is to align the UWP version with the existing library.\
We have deliberately chosen not to support a lower Windows version even though the Uno Platform can handle the creators fall update (16299).

![](/images/2020-09-27-30.png)

I set the target version to Windows 10 1903 (18362) and the minimum version to Windows 10 1803 (17134).\
Rebuild the UWP sample project to confirm that everything is still working as it should.\
So far so good.

# Add preexisting code to sample
I'm now going to add the [code](https://github.com/I-Synergy/I-Synergy.Framework/tree/master/samples/I-Synergy.Framework.Windows.Samples) from my previous sample to the new sample project.

![](/images/2020-09-29.png)

This is a simple UWP project that includes navigation, Mvvm base classes, various dependency injectable services and a slideshow viewer that serve as a base for most of my applications. Also, you can see that the old namespace (ISynergy.Framework.Windows.Samples) is used here. This will be not a real issue because we are going to remove it when we are done.

I think it would be useful to first put the Appx.xaml and Assets in the shared project and put the rest in the UWP project to have a buildable project and only move the remaining code to the shared project after the migration of the framework project. I would also throw away the Assets folder in the UWP head.

Another folder I will remove is the Strings folder in the UWP project. This is because I use another way to implement translated resources. I'll use my own language service implementation.

The MainPage can also be removed because our view exists in the Mvvm folder structure.

What you also will see is that many service implementations actually do nothing because they  serves primarily as an example and are not functional.\
If you look at the code carefully, you will quickly understand this.

Ow yes, remember that the namespaces should of course be correct in the actual UI sample project after migrating the code. 😉

What you will see now is that there are quite a lot errors and this is because we are missing some references.\
Normally they come along with the I-Synergy.Framework.UI.Windows project only we can't use it now.

![](/images/2020-09-29-5.png)

I don't know why, but I think because this library is not yet suitable for Uno Platform applications.\
We're going to fix this in the next chapter. For now I check-in everything, so you can see from the [commit](https://github.com/I-Synergy/I-Synergy.Framework/commit/b4473181d4276ce900d5a9562891fb4245e34563) what the changes are for yourself.

Cheers.