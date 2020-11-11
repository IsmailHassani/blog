+++
categories = ["Software Development"]
date = "2020-10-29T20:51:19+02:00"
description = "Running the Android application for the first time"
draft = false
series = ["Uno Platform"]
tags = ["Uno Platform", "UWP", "C#", ".Net", "Visual Studio", "Microsoft", "I-Synergy"]
title = "Uno Platform - Part 7"

+++
Hi folks,\
Today (on my birthday, October 29th) I started fixing bugs on the Android head.\
After all, the goal of this transition is to be able to run my applications on Android as well.\
I'm very curious.

# DataContextChanged event
The signature of the event handler is different here than with UWP.\
I added an #if directive and changed it to the right one.
```
#if NETFX_CORE
private void ThemeWindow_DataContextChanged(FrameworkElement sender, DataContextChangedEventArgs args)
{
    ...
}
#else
private void ThemeWindow_DataContextChanged(DependencyObject sender, DataContextChangedEventArgs args)
{
    ...
}
#endif
```

# KeyboardAccelerator
It is not supported by Android so disabled with an #if directive.\
I do wonder here: What if I use an Android system with a keyboard attached to it?
```
/// <summary>
/// Handles the Loaded event of the RootNavigationView control.
/// </summary>
/// <param name="sender">The source of the event.</param>
/// <param name="e">The <see cref="RoutedEventArgs"/> instance containing the event data.</param>
private void RootNavigationViewLoaded(object sender, RoutedEventArgs e)
{
#if NETFX_CORE
    // add keyboard accelerators for backwards navigation
    var GoBack = new KeyboardAccelerator
    {
        Key = VirtualKey.GoBack
    };

    GoBack.Invoked += BackInvoked;

    var AltLeft = new KeyboardAccelerator
    {
        Key = VirtualKey.Left
    };

    AltLeft.Invoked += BackInvoked;

    KeyboardAccelerators.Add(GoBack);
    KeyboardAccelerators.Add(AltLeft);

    // ALT routes here
    AltLeft.Modifiers = VirtualKeyModifiers.Menu;
#endif
}
```

# System.Reflection.TargetInvocationException
The problem with this error message is that it is quite brief. At [StackOverflow](https://stackoverflow.com/search?q=TargetInvocationException) you will see that many people have problems with this. Especially since it's hard to retrieve the actual exception out of it. The application crashes or gives you a very generic error message, but you won't get much wiser.\
We do know where the error occurs in the stacktrace. By putting this in a **try-catch** and setting up a break point here, we can read out the actual error as we're debugging.\
Maybe one day I'll have to adjust the global error handler to do this for me.\
What turns out? A number of exceptions are thrown.

A reason why some errors were not dealt with was also the fact that the event subscription only happened at the end of the constructor.
By doing the subscription immediately after the DI regitration, these exceptions are caught.
- **Package.Current.Installed­Location.Path**\
    The first exception is in App.xaml.cs on the ConfigureServices subroutine. Here, as with .Net Core, I configure my dependency injection. Because I need the assembly of the executing application, I want to get the path of the package.  
    However, this feature is not supported by Uno because it is UWP specific, causing it to fail.\
    Solution is to use the [Assembly.GetAssembly(typeof(T))](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.getassembly?view=netcore-3.1).
- **appsettings.json**\
    Now that we've retrieved the assembly information, Android still can't read the file.\
    I use the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) for this, but the AddJsonFile feature doesn't work.\
    I can put the asppsettings file separately in the Assets directory, but I want this generically for all heads to be the same. Fortunately, we can read the file also as a stream from [Assembly.GetManifestResourceStream](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.assembly.getmanifestresourcestream?view=netcore-3.1)
```
protected override Assembly GetEntryAssembly() => 
    Assembly.GetAssembly(typeof(App));

protected override void ConfigureServices(IServiceCollection services)
{
    base.ConfigureServices(services);

    var assembly = GetEntryAssembly();

    ConfigurationRoot = new ConfigurationBuilder()
        .AddJsonStream(assembly.GetManifestResourceStream($"{assembly.GetName().Name}.appsettings.json"))
        .Build();

    ...
}
```

# Assembly.Load
Till now, I've used [Assembly.Load](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.assembly.load?view=netcore-3.1) to get the assembly by the assembly path.\
But as indicated in the previous example, this doesn't really work on Android because the assembly isn't found. This gives you a FileNotFoundException. 
The alternative is to use the type to pick up the assembly using the [Assembly.GetAssembly(typeof(T))](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.assembly.getassembly?view=netcore-3.1). In order not to make a strange or illogical reference to a class, I have made in my libraries a kind of dummy identifier that I can use for this.

```
/// <summary>
/// Assembly Identifier.
/// </summary>
public class MvvmAssemblyIdentifier
{
}
```

It doesn't do anything, but I can apply it clearly recognizable.
```
assemblies.Add(Assembly.GetAssembly(typeof(MvvmAssemblyIdentifier)));
assemblies.Add(Assembly.GetAssembly(typeof(UIAssemblyIdentifier)));
```

# Theme colors
In UWP I had defined a number of theme colors in separate resource dictionaries. These were loaded at runtime from the color set.\
What that thing did was actually set the application's **SystemAccentColor** to a fixed predefined value.\
Instead of dynamically loading resource dictionaries, I've now customized the ThemeSelectorService to match the **SystemAccentColor** to the color you want.

# PersonPicture
It turns out that if the Initials or DisplayName property is empty or contains a null value, from for example the viewmodel, the control crashes.
If I give both a value, this problem is gone.

It seems that this is related to an issue already known to Uno on [GitHub](https://github.com/unoplatform/uno/issues/3591)

# The application still crashes at startup
After all the previous issues solved, the application still crashes. I had tried a lot of things, but still get that annoying **TargetInvokationException**.
```
at System.Reflection.RuntimeConstructorInfo.InternalInvoke (System.Object obj, System.Object[] parameters, System.Boolean wrapExceptions) [0x00018] in /Users/builder/jenkins/workspace/archive-mono/2020-02/android/release/mcs/class/corlib/System.Reflection/RuntimeMethodInfo.cs:944 
  at System.Reflection.RuntimeConstructorInfo.DoInvoke (System.Object obj, System.Reflection.BindingFlags invokeAttr, System.Reflection.Binder binder, System.Object[] parameters, System.Globalization.CultureInfo culture) [0x00086] in /Users/builder/jenkins/workspace/archive-mono/2020-02/android/release/mcs/class/corlib/System.Reflection/RuntimeMethodInfo.cs:926 
  at System.Reflection.RuntimeConstructorInfo.Invoke (System.Reflection.BindingFlags invokeAttr, System.Reflection.Binder binder, System.Object[] parameters, System.Globalization.CultureInfo culture) [0x00000] in /Users/builder/jenkins/workspace/archive-mono/2020-02/android/release/mcs/class/corlib/System.Reflection/RuntimeMethodInfo.cs:960 
  at System.Reflection.ConstructorInfo.Invoke (System.Object[] parameters) [0x00000] in /Users/builder/jenkins/workspace/archive-mono/2020-02/android/release/external/corefx/src/Common/src/CoreLib/System/Reflection/ConstructorInfo.cs:22 
  at Microsoft.Extensions.DependencyInjection.ServiceLookup.CallSiteRuntimeResolver.VisitConstructor (Microsoft.Extensions.DependencyInjection.ServiceLookup.ConstructorCallSite constructorCallSite, Microsoft.Extensions.DependencyInjection.ServiceLookup.RuntimeResolverContext context) [0x00046] in <dbdeee8b7a9f431c91bdfa85d8dca11c>:0 
```
And the Inner exception:
```
at Windows.ApplicationModel.Resources.ResourceLoader+<GetCulturesHierarchy>d__32.MoveNext () [0x0006c] in C:\a\1\s\src\Uno.UWP\ApplicationModel\Resources\ResourceLoader.cs:190 
  at System.Linq.Set`1[TElement].UnionWith (System.Collections.Generic.IEnumerable`1[T] other) [0x00018] in /Users/builder/jenkins/workspace/archive-mono/2020-02/android/release/external/corefx/src/System.Linq/src/System/Linq/Set.cs:206 
  at System.Linq.Enumerable+DistinctIterator`1[TSource].FillSet () [0x0000b] in /Users/builder/jenkins/workspace/archive-mono/2020-02/android/release/external/corefx/src/System.Linq/src/System/Linq/Distinct.cs:95 
  at System.Linq.Enumerable+DistinctIterator`1[TSource].ToArray () [0x00000] in /Users/builder/jenkins/workspace/archive-mono/2020-02/android/release/external/corefx/src/System.Linq/src/System/Linq/Distinct.cs:99 
  at System.Linq.Enumerable.ToArray[TSource] (System.Collections.Generic.IEnumerable`1[T] source) [0x0000e] in /Users/builder/jenkins/workspace/archive-mono/2020-02/android/release/external/corefx/src/System.Linq/src/System/Linq/ToCollection.cs:18 
  at Windows.ApplicationModel.Resources.ResourceLoader.ReloadResources (System.Globalization.CultureInfo current, System.String defaultLanguage) [0x00005] in C:\a\1\s\src\Uno.UWP\ApplicationModel\Resources\ResourceLoader.cs:219 
  at Windows.ApplicationModel.Resources.ResourceLoader.EnsureLoadersCultures () [0x00021] in C:\a\1\s\src\Uno.UWP\ApplicationModel\Resources\ResourceLoader.cs:209 
  at Windows.ApplicationModel.Resources.ResourceLoader.GetString (System.String resource) [0x00044] in C:\a\1\s\src\Uno.UWP\ApplicationModel\Resources\ResourceLoader.cs:65 
  at Uno.UI.Helpers.WinUI.ResourceAccessor.GetLocalizedStringResource (System.String resourceName) [0x00000] in C:\a\1\s\src\Uno.UI\Helpers\WinUI\ResourceAccessor.cs:59 
  at Windows.UI.Xaml.Controls.NavigationView.SetPaneToggleButtonAutomationName () [0x00008] in C:\a\1\s\src\Uno.UI\UI\Xaml\Controls\NavigationView\NavigationView.cs:847 
  at Windows.UI.Xaml.Controls.NavigationView.OnApplyTemplate () [0x000e2] in C:\a\1\s\src\Uno.UI\UI\Xaml\Controls\NavigationView\NavigationView.cs:162 
  at Windows.UI.Xaml.Controls.Control.set_TemplatedRoot (Android.Views.View value) [0x0006a] in C:\a\1\s\src\Uno.UI\UI\Xaml\Controls\Control\Control.cs:167 
  at Windows.UI.Xaml.Controls.Control.UpdateTemplate () [0x0003e] in C:\a\1\s\src\Uno.UI\UI\Xaml\Controls\Control\Control.cs:425 
  at Windows.UI.Xaml.Controls.Control.SetUpdateControlTemplate (System.Boolean forceUpdate) [0x0001c] in C:\a\1\s\src\Uno.UI\UI\Xaml\Controls\Control\Control.cs:128 
  at Windows.UI.Xaml.Controls.Control.OnTemplateChanged (Windows.UI.Xaml.DependencyPropertyChangedEventArgs e) [0x00007] in C:\a\1\s\src\Uno.UI\UI\Xaml\Controls\Control\Control.cs:110 
  at Windows.UI.Xaml.Controls.Control+<>c.<.cctor>b__338_0 (Windows.UI.Xaml.DependencyObject s, Windows.UI.Xaml.DependencyPropertyChangedEventArgs e) [0x00000] in C:\a\1\s\src\Uno.UI\UI\Xaml\Controls\Control\Control.cs:105 
  at Windows.UI.Xaml.PropertyMetadata.RaisePropertyChanged (Windows.UI.Xaml.DependencyObject source, Windows.UI.Xaml.DependencyPropertyChangedEventArgs e) [0x00000] in C:\a\1\s\src\Uno.UI\UI\Xaml\PropertyMetadata.cs:187 
  at Windows.UI.Xaml.DependencyObjectStore.InvokeCallbacks (Windows.UI.Xaml.DependencyObject actualInstanceAlias, Windows.UI.Xaml.DependencyProperty property, Windows.UI.Xaml.DependencyPropertyDetails propertyDetails, System.Object previousValue, Windows.UI.Xaml.DependencyPropertyValuePrecedences previousPrecedence, System.Object newValue, Windows.UI.Xaml.DependencyPropertyValuePrecedences newPrecedence, System.Boolean bypassesPropagation) [0x000e3] in C:\a\1\s\src\Uno.UI\UI\Xaml\DependencyObjectStore.cs:1552 
  at Windows.UI.Xaml.DependencyObjectStore.RaiseCallbacks (Windows.UI.Xaml.DependencyObject actualInstanceAlias, Windows.UI.Xaml.DependencyPropertyDetails propertyDetails, System.Object previousValue, Windows.UI.Xaml.DependencyPropertyValuePrecedences previousPrecedence, System.Object newValue, Windows.UI.Xaml.DependencyPropertyValuePrecedences newPrecedence) [0x0005b] in C:\a\1\s\src\Uno.UI\UI\Xaml\DependencyObjectStore.cs:1461 
  at Windows.UI.Xaml.DependencyObjectStore.InnerSetValue (Windows.UI.Xaml.DependencyProperty property, System.Object value, Windows.UI.Xaml.DependencyPropertyValuePrecedences precedence, Windows.UI.Xaml.DependencyPropertyDetails propertyDetails) [0x00195] in C:\a\1\s\src\Uno.UI\UI\Xaml\DependencyObjectStore.cs:478 
  at Windows.UI.Xaml.DependencyObjectStore.SetValue (Windows.UI.Xaml.DependencyProperty property, System.Object value, Windows.UI.Xaml.DependencyPropertyValuePrecedences precedence, Windows.UI.Xaml.DependencyPropertyDetails propertyDetails) [0x0002e] in C:\a\1\s\src\Uno.UI\UI\Xaml\DependencyObjectStore.cs:417 
  at Windows.UI.Xaml.DependencyObjectStore.SetValue (Windows.UI.Xaml.DependencyProperty property, System.Object value) [0x00000] in C:\a\1\s\src\Uno.UI\UI\Xaml\DependencyObjectStore.cs:381 
  at Uno.UI.Controls.BindableView.SetValue (Windows.UI.Xaml.DependencyProperty dp, System.Object value) [0x00000] in C:\a\1\s\src\Uno.UI\obj\Release\MonoAndroid10.0\g\DependencyObjectGenerator\Uno_UI_Controls_BindableView_eefac468be1af0c618115e95339af76c.g.cs:47 
  at Windows.UI.Xaml.Setter.ApplyTo (Windows.UI.Xaml.DependencyObject o) [0x0004b] in C:\a\1\s\src\Uno.UI\UI\Xaml\Setter.cs:95 
  at Windows.UI.Xaml.Style.ApplyTo (Windows.UI.Xaml.DependencyObject o, Windows.UI.Xaml.DependencyPropertyValuePrecedences precedence) [0x0003f] in C:\a\1\s\src\Uno.UI\UI\Xaml\Style\Style.cs:70 
  at Windows.UI.Xaml.FrameworkElement.OnStyleChanged (Windows.UI.Xaml.Style oldStyle, Windows.UI.Xaml.Style newStyle, Windows.UI.Xaml.DependencyPropertyValuePrecedences precedence) [0x00011] in C:\a\1\s\src\Uno.UI\UI\Xaml\FrameworkElement.cs:429 
  at Windows.UI.Xaml.FrameworkElement.ApplyDefaultStyle () [0x0001c] in C:\a\1\s\src\Uno.UI\UI\Xaml\FrameworkElement.cs:447 
  at Windows.UI.Xaml.FrameworkElement.ApplyStyles () [0x00006] in C:\a\1\s\src\Uno.UI\UI\Xaml\FrameworkElement.cs:295 
  at Windows.UI.Xaml.FrameworkElement.CreationComplete () [0x00014] in C:\a\1\s\src\Uno.UI\UI\Xaml\FrameworkElement.cs:313 
  at ISynergy.Framework.UI.Sample.Views.ShellView+<>c__DisplayClass8_0.<InitializeComponent>b__6 (Windows.UI.Xaml.Controls.NavigationView c25) [0x001b0] in C:\Projects\I-Synergy.Framework\samples\ISynergy.Framework.UI.Sample\ISynergy.Framework.UI.Sample.Droid\obj\Debug\100\g\XamlCodeGenerator\ShellView_efa32789aed01da4165261ebec152077.g.cs:446 
  at ISynergy.Framework.UI.Sample.Droid.ShellView_efa32789aed01da4165261ebec152077XamlApplyExtensions.ShellView_efa32789aed01da4165261ebec152077_XamlApply (Windows.UI.Xaml.Controls.NavigationView instance, ISynergy.Framework.UI.Sample.Droid.ShellView_efa32789aed01da4165261ebec152077XamlApplyExtensions+XamlApplyHandler14 handler) [0x00001] in C:\Projects\I-Synergy.Framework\samples\ISynergy.Framework.UI.Sample\ISynergy.Framework.UI.Sample.Droid\obj\Debug\100\g\XamlCodeGenerator\ShellView_efa32789aed01da4165261ebec152077.g.cs:898 
  at ISynergy.Framework.UI.Sample.Views.ShellView.InitializeComponent () [0x000b2] in C:\Projects\I-Synergy.Framework\samples\ISynergy.Framework.UI.Sample\ISynergy.Framework.UI.Sample.Droid\obj\Debug\100\g\XamlCodeGenerator\ShellView_efa32789aed01da4165261ebec152077.g.cs:82 
  at ISynergy.Framework.UI.Sample.Views.ShellView..ctor () [0x00076] in C:\Projects\I-Synergy.Framework\samples\ISynergy.Framework.UI.Sample\ISynergy.Framework.UI.Sample.Shared\Views\ShellView.xaml.cs:28 
  at (wrapper managed-to-native) System.Reflection.RuntimeConstructorInfo.InternalInvoke(System.Reflection.RuntimeConstructorInfo,object,object[],System.Exception&)
  at System.Reflection.RuntimeConstructorInfo.InternalInvoke (System.Object obj, System.Object[] parameters, System.Boolean wrapExceptions) [0x00005] in /Users/builder/jenkins/workspace/archive-mono/2020-02/android/release/mcs/class/corlib/System.Reflection/RuntimeMethodInfo.cs:936 
```

For two weeks, I've been pulling my hair out. Because the error was so unclear, I even tried another Dependecy Injector from [DryIoC](https://github.com/dadhi/DryIoc) to see if I could get more information about the exception.\
Finally, i decided to post an issue on [GitHub](https://github.com/unoplatform/uno/issues/4456).

To my astonishment, the solution turns out to be very simple.\
I've apparently missed this somewhere in the documentation, but by adding **InitializeComponent** in the App constructor, this problem is gone and I can finally run the application.\
To be clear, you need this in the Android head, otherwise it just won't run.
```
public App() : base() 
{
#if __ANDROID__
    InitializeComponent();
#endif
}
```
# We have a lift off... or not
Now that I can start the application, I can finally test the functionality of the application.

![](/images/2020-11-10.png)

Everything seems to work...

# Cleartext HTTP traffic not permitted
The cause of the problem is that Android doesn't allow HTTP access by default. Accessing HTTPS is no problem, but if the site doesn't support HTTPS, or if you need to access HTTPS, you should handle this.
According to [Opt out of cleartext traffic in Android Developer](https://developer.android.com/training/articles/security-config#CertificatePinning), Starting with Android 9 (API level 28), cleartext support is disabled by default.
So, you have to configure about cleartext HTTP to get access to HTTP sites with API level 28 or above.

## Allow all connections to HTTP URLs
You can allow all connections to HTTP URLs if you set the UsesCleartextTraffic property to true in Main.cs **ApplicationAttribute**.
```
[global::Android.App.ApplicationAttribute(
    Label = "@string/ApplicationName",
    LargeHeap = true,
    HardwareAccelerated = true,
    Theme = "@style/AppTheme",
    UsesCleartextTraffic = true
)]
```

## Allow connections to specific sites.
If you want to allow connections to specific sites. then make a /res/xml/network_security_config.xml file and add some sites.
if you add a domain-config like below, you can allow connections to secure.example.com.
```
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="false">
        <domain includeSubdomains="true">secure.example.com</domain>
    </domain-config>
</network-security-config>
```
Also, you have to let the system know where the config file is. Add following code to your Main.cs **ApplicationAttribute**.
```
[global::Android.App.ApplicationAttribute(
    Label = "@string/ApplicationName",
    LargeHeap = true,
    HardwareAccelerated = true,
    Theme = "@style/AppTheme",
    NetworkSecurityConfig="@xml/network_security_config"
)]
```

# No Animation on start slideshow
Animations I use in the slideshow (fade in and out) don't seem to work.
I used a kind of staging system where the images are loaded into another Image control and are faded in and out.\
This doesn't seem to work on Android.
It's unfortunate, but again this animation is a nice to have.
```
/// <summary>
/// Sources the changed.
/// </summary>
/// <param name="dependencyObject">The dependency object.</param>
/// <param name="dependencyPropertyChangedEventArgs">The <see cref="DependencyPropertyChangedEventArgs"/> instance containing the event data.</param>
private static void SourceChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs dependencyPropertyChangedEventArgs)
{
    var control = (ImageView)dependencyObject;
    var newSource = (ImageSource)dependencyPropertyChangedEventArgs.NewValue;

    System.Diagnostics.Debug.WriteLine("Image source changed: {0}", ((BitmapImage)newSource).UriSource.AbsolutePath);

    if (newSource != null)
    {
        var image = (BitmapImage)newSource;

        // If the image is not a local resource or it was not cached
        if (image.UriSource.Scheme != "ms-appx" && image.UriSource.Scheme != "ms-resource" && (image.PixelHeight * image.PixelWidth == 0))
        {
#if NETFX_CORE
            image.ImageOpened += (sender, args) => control.LoadImage(image);
            control.Staging.Source = image;
#else
            control.Image.Source = image;
#endif
        }
        else
        {
#if NETFX_CORE
            control.LoadImage(newSource);
#else
            control.Image.Source = image;
#endif
        }
    }
}
```
![](/images/2020-11-10-1.png)
# Conclusion
Now we are 2 weeks further and I have to say that I was initially skeptical about the often mentioned ease to make a cross-platform application.\
In my eyes, it could never be an easy 1-2-3. Unless you had a simple "Hello world" application.

Due to the many components (internal and external) you often run into errors that are initially meaningless, but also against limitations of the Uno platform.\
Not all controls are available or can only be used partially. Some are surpassable like the animations, while others are essential.
For example, in the [previous episode](https://ismail.hassani.eu/posts/2020/10/19/unoplatform_part6/) I talked about the FilePicker. It appears to work very fine in Android.
This means that we can also use parts or solutions that work in Xamarin.

I will have to do some more tweaks about the styling and some minor issues related to this, but all in all, the entire conversion turns out to have gone pretty smoothly.
The main obstacle is the scarce documentation. But you also have to be a little creative here./
If you still can't figure it out, there are of course always the great people from the Uno platform who can help you.

![](/images/uno32.jpg)

Don't forget that developments on this platform now follow each other very quickly.
[Yesterday (November 9, 2020) version 3.2 of the Uno Platform was released](https://platform.uno/blog/uno-platform-3-2-net-5-c-9-support-and-net-5-webassembly-aot-support/) where new functionalities and goodies have been added.

In the next episode, I'm going to talk about the conversion to WebAssembly. From what I've seen, this will also be a fun challenge.

Till next time, stay safe and wear a mask.