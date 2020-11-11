+++
categories = ["Software Development"]
date = "2020-10-08T20:18:59+02:00"
description = "Testing and fixing bugs after migration"
draft = false
series = ["Uno Platform"]
tags = ["Uno Platform", "UWP", "C#", ".Net", "Visual Studio", "Microsoft", "I-Synergy"]
title = "Uno Platform - Part 4"

+++
Today I'm going to test the migration to see if everything still works as it should.

This is an important step because this is the only way to test if we broke anything.\
In the [previous episode](https://ismail.hassani.eu/posts/2020/10/01/unoplatform_part3/) we did the migration and made the projects and sample buildable. This has been the first step in seeing if we have made any serious mistakes.

This episode is going to see if we can run the program at all. The errors that can and probably will occur here are often called runtime errors. You won't see these until you run the program.

It is also important to realize yourself to have the UWP head working before we start treating the other heads (android and web assembly). After all, we know that our starting point was a working UWP application.

# Starting the sample
We're going to start running the sample project. The easiest thing is to set this project as a startup project.

Click on the project with the right mouse and select "Set as startup project".\
Once you've done this, you can start debugging with F5.

Let's try this and see what happens.

# Errors!
![](/images/2020-10-03.png)

![](/images/2020-10-03-1.png)

I'll put a break point here and see what he's trying to find.\
Apparently, the ```_services.GetType().GetMethods()``` does not have a single value that meets the query. The implementation type I want to register here is that of the type ```SlideShowViewModel```.

This function attempts to register a singleton in the dependency injection via delegate of the servicecollection. But because ```AddSingleton ```is an extension method, it is not recognized.\
That's why I'm going to use the regular implementation of the extension here.\
I don't know why in God's name I did this. Maybe we'll find the reason along the way.

![](/images/2020-10-03-2.png)

Once we've done this, and rebuild, we'll run into the next exception.

![](/images/2020-10-03-3.png)

```
Exception thrown: 'System.ArgumentNullException' in Microsoft.Extensions.DependencyInjection.Abstractions.dll
An exception of type 'System.ArgumentNullException' occurred in Microsoft.Extensions.DependencyInjection.Abstractions.dll but was not handled in user code
Value cannot be null.</pre>
```

Here is an attempt to get an instance of the ```INavigationService``` out of the _serviceProvider.\
However, it is still null because it has not yet been built in line 83.\
As a result, the navigation service is not yet resolvable.

Since this is the entry point of our application, I have to look at where the instance of this service is created and return it.

![](/images/2020-10-03-5.png)

On line 367, we have the ```ConfigureServices``` method where this service is registered.\
By creating the NavigationService earlier in the call stack and giving registering the instance, we can also use it earlier in our code.\
<a rel="noreferrer noopener" href="https://github.com/I-Synergy/I-Synergy.Framework/commits/features/uno_plaform/src/ISynergy.Framework.UI/Application/BaseApplication.cs" target="_blank">In the code, you can see what I've changed here.</a>

# ServiceLocator
Previously, I have always used a Service Locator for parts where dependency injection was not available. Think about extensions, behavior and converter classes or XAML code-behind code.\
Apart from being an anti-pattern, this seemed a real problem in the past in the WebAssembly head.\
This forced me to adjust the Service Locator to be more dependent on the <a rel="noreferrer noopener" href="https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection" target="_blank">Microsoft.Extension.DependecyInjection</a> library. Again, if used, use it wisely.

After solving this problem, we'll see what next problem that will occur.

![](/images/2020-10-03-6.png)

```
Cannot locate resource from 'ms-appx:///ISynergy.Framework.UI/Controls/Validation/ControlWrapper.xaml'. [Line: 12 Position: 37]
The program '[25848] ISynergy.Framework.UI.Sample.exe: Program Trace' has exited with code 0 (0x0).
The program '[25848] ISynergy.Framework.UI.Sample.exe' has exited with code -1073741189 (0xc000027b).
Activation of the Windows Store app 'b3bccab0-e662-4443-8af3-63721f4dadb5_xzkwh72366gz4!App' failed with error 'Operation not supported. Unknown error: 0x80040904'.
```

This refers to a piece of code I have in the App.xaml.\
If we look in that code, we see that this line is somewhere between the other lines. This also means that the previous lines have ran successfully.\
Controls/Validation/ControlWrapper as the error suggests is a control that I used in the past but have since deleted in favor of the UWP Toolkit. So this line can be deleted.

After running the application I'm presented with an graphical user interface and "everything seems to work".

![](/images/2020-10-03-7.png)

Let's press the theme button and see what happens.

![](/images/tenor.gif)

There is a null reference exception in the ```UIVisualizerService```. This service is responsible for displaying Dialog Windows.\
The service locator is trying to resolve the ```IThemeWindow``` instance here. But because the implementation (the real ```ThemeWindow```) is not available in this project, this reference is incorrect.\
Let's see if I can add it from my original project.\
Except for this window, there are a number of other windows that I also have to add here to avoid the same exception.
- ```LanguageWindow``` (UI language selection window)
- ```NoteWindow``` (Window for notes and memos, not used in this sample)
- ```SelectionWindow``` (not used in this sample)
- ```ThemeWindow``` (Color Table Selection Screen)

After adding these missing windows, the buttons work. Sweet!

If we now try to actually change the theme color, the program crashes and we'll see the following error message:

```
The thread 0x7d00 has exited with code 0 (0x0).
The thread 0x6d0c has exited with code 0 (0x0).
The parameter is incorrect.
The program '[24284] ISynergy.Framework.UI.Sample.exe' has exited with code -1073741189 (0xc000027b).
The program '[24284] ISynergy.Framework.UI.Sample.exe: Program Trace' has exited with code 0 (0x0).
```

Well, slap me silly, but after a search of almost a day, the crash appears to be a catastrophic error resulted from storing the theme color in the application settings and trying to reset the application theme. What in hell did I try to accomplish here. Clearly a case working too late at night.

I've modified and simplified the entire procedure to fix the error.

The program is now running as it should. Nice!

[![See video](http://img.youtube.com/vi/YR_tCN2Y6ho/0.jpg)](http://www.youtube.com/watch?v=YR_tCN2Y6ho)