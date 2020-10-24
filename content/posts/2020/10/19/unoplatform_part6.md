---
categories:
- Software Development
date: "2020-10-19T20:51:19+02:00"
description: Fixing the dreadfull not implemented in Uno warnings
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
title: Uno Platform - Part 6
---
After creating a nuget library, now is the time to clean up the code further.\
As I said before, there seem to be quite a few warnings generated during the build.\
We are now particularly interested in the warnings related to the Uno Platform.\
I'll focus on the build of the UAP head first because this should be runnable as used to be.

After that, i will head over to the Android build and address the problems in a following blog post.

# Update Uno Platform 3.1
October 23, 2020: A new version of the Uno Platform has also been released today. This has added a lot of new things, including support for Linux. Don't forget to update your Visual Studio extensions too.

I recommend you to take a look at their [release notes](https://github.com/unoplatform/uno/releases/) and [blog post](https://platform.uno/blog/uno-platform-3-1-released-linux-new-winui-controls-prism-8-0-and-more/).

![](/images/uno31.jpg)

# FileOpenPicker and FileSavePicker  
```
2020-10-13T07:32:10.7972623Z Helpers\UploadImageHelper.cs(20,26): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7976211Z Helpers\UploadImageHelper.cs(22,28): warning Uno0001: Windows.Storage.Pickers.PickerViewMode.Thumbnail is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7980064Z Helpers\UploadImageHelper.cs(26,13): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.FileTypeFilter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7983605Z Helpers\UploadImageHelper.cs(27,13): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.FileTypeFilter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7991512Z Helpers\UploadImageHelper.cs(28,13): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.FileTypeFilter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7998252Z Helpers\UploadImageHelper.cs(29,13): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.FileTypeFilter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8004880Z Helpers\UploadImageHelper.cs(30,13): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.FileTypeFilter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8014855Z Helpers\UploadImageHelper.cs(32,30): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.PickSingleFileAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8020732Z Helpers\UploadImageHelper.cs(52,19): warning Uno0001: Windows.Storage.StorageFile.CopyAndReplaceAsync(Windows.Storage.IStorageFile) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8074666Z Helpers\ImagePicker.cs(32,26): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8081070Z Helpers\ImagePicker.cs(34,28): warning Uno0001: Windows.Storage.Pickers.PickerViewMode.Thumbnail is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8088537Z Helpers\ImagePicker.cs(38,13): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.FileTypeFilter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8092341Z Helpers\ImagePicker.cs(39,13): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.FileTypeFilter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8096337Z Helpers\ImagePicker.cs(40,13): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.FileTypeFilter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8100084Z Helpers\ImagePicker.cs(41,13): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.FileTypeFilter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8103963Z Helpers\ImagePicker.cs(42,13): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.FileTypeFilter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8107797Z Helpers\ImagePicker.cs(44,30): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.PickSingleFileAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8111258Z Helpers\ImagePicker.cs(60,27): warning Uno0001: Windows.Storage.StorageFile.ContentType is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8115374Z Helpers\ImagePicker.cs(66,19): warning Uno0001: Windows.Storage.StorageFile.CopyAndReplaceAsync(Windows.Storage.IStorageFile) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8119897Z Helpers\ImagePicker.cs(69,44): warning Uno0001: Windows.Storage.StorageFile.OpenReadAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8123688Z Helpers\ImagePicker.cs(70,32): warning Uno0001: Windows.Storage.Streams.IRandomAccessStream.AsStream() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8127239Z Helpers\ImagePicker.cs(71,35): warning Uno0001: Windows.Storage.Streams.IRandomAccessStream.Size is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8448092Z Dialogs\Folder\FolderPickerWrapper.cs(26,22): warning Uno0001: Windows.Storage.Pickers.FolderPicker is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8451406Z Dialogs\Folder\FolderPickerWrapper.cs(36,17): warning Uno0001: Windows.Storage.Pickers.FolderPicker.FileTypeFilter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8454937Z Dialogs\Folder\FolderPickerWrapper.cs(45,76): warning Uno0001: Windows.Storage.Pickers.FolderPicker.PickSingleFolderAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8460703Z Dialogs\FileSave\FileSavePickerWrapper.cs(26,22): warning Uno0001: Windows.Storage.Pickers.FileSavePicker is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8465180Z Dialogs\FileSave\FileSavePickerWrapper.cs(39,17): warning Uno0001: Windows.Storage.Pickers.FileSavePicker.FileTypeChoices is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8468643Z Dialogs\FileSave\FileSavePickerWrapper.cs(51,70): warning Uno0001: Windows.Storage.Pickers.FileSavePicker.PickSaveFileAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8472147Z Dialogs\FileOpen\FileOpenPickerWrapper.cs(27,22): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8475688Z Dialogs\FileOpen\FileOpenPickerWrapper.cs(37,17): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.FileTypeFilter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8479539Z Dialogs\FileOpen\FileOpenPickerWrapper.cs(46,72): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.PickSingleFileAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8483858Z Dialogs\FileOpen\FileOpenPickerWrapper.cs(55,90): warning Uno0001: Windows.Storage.Pickers.FileOpenPicker.PickMultipleFilesAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
As you can see, this list is full of errors related to the FileOpenPicker and FileSavePicker.\
This has also been the biggest challenge to fix. I have also inherited a legacy of earlier code which now mainly increases the amount of errors.\
I also found the information about this part very limited.\
I couldn't find much, except that people are still working on this at Uno.

How do I solve this problem?\
In some places I need a file picker. For example, to store or upload documents and templates.

That's when I realized it might be possible to take a detour.\
Xamarin does have file picker support for Android, iOS and UWP. Can't I use something for that?

This led me to a search on [Nuget](https://www.nuget.org/packages?q=File+picker) and came across an old project by [Gerald Versluis](https://github.com/jfversluis/FilePicker-Plugin-for-Xamarin-and-Windows) where he created a nuget package for this very problem.\
Because this repository is no longer maintained and redirects to [Xamarin.Essentials](https://docs.microsoft.com/en-us/xamarin/essentials/), I tried to use this new library, but it's not recognized by my own code, which makes it pretty useless.\
That's why I used the code of Gerald to create my own implementation for this functionality.

The trick is to use some Xamarin libraries to be able to use them in the Uno libarary.
- [Xamarin.AndroidX.Legacy.Support.v4](https://www.nuget.org/packages/Xamarin.AndroidX.Legacy.Support.V4/)
- [Xamarin.AndroidX.AppCompat](https://www.nuget.org/packages/Xamarin.AndroidX.AppCompat/)
- [Xamarin.AndroidX.Fragment](https://www.nuget.org/packages/Xamarin.AndroidX.Fragment/)

I added these reference like shown below.
```
<ItemGroup Condition="'$(TargetFramework)' == 'MonoAndroid90'">
    <PackageReference Include="Xamarin.AndroidX.Legacy.Support.v4" Version="1.0.0.5" />
    <PackageReference Include="Xamarin.AndroidX.AppCompat" Version="1.2.0.4" />
    <PackageReference Include="Xamarin.AndroidX.Fragment" Version="1.2.5.3" />
</ItemGroup>

<ItemGroup Condition="'$(TargetFramework)' == 'MonoAndroid10.0'">
    <PackageReference Include="Xamarin.AndroidX.Legacy.Support.v4" Version="1.0.0.5" />
    <PackageReference Include="Xamarin.AndroidX.AppCompat" Version="1.2.0.4" />
</ItemGroup>
```
Check in my repositiry the FileService and the FilePicker control to see what i've done.\
For now it works.

![](/images/2020-10-24-1.png)

# Windows.ApplicationModel.Package
```
2020-10-13T07:32:10.7549465Z Services\InfoService.cs(33,24): warning Uno0001: Windows.ApplicationModel.Package.PublisherDisplayName is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7576277Z Services\InfoService.cs(69,24): warning Uno0001: Windows.ApplicationModel.Package.Description is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
In UWP you can use this class to get information about your installed package. It seems that these properties are not available in heads other then UWP.\
I don't understand why I try to retrieve all the assembly information from the package, but the only one I need is the package location. For the rest, I can just use FileVersionInfo.\
To test if this actually works, we can use our sample projects.

I have added a new ViewModel, View and menuitem to show this information if it works.\
Let's try!

![](/images/2020-10-24.png)

It works in the UAP head.\
Before i can test it on other heads i first have to solve the other build errors.

# ApplicationViewTitleBar
```
2020-10-13T07:32:10.7594347Z Services\ThemeSelectorService.cs(107,17): warning Uno0001: Windows.ApplicationModel.Core.CoreApplicationViewTitleBar.ExtendViewIntoTitleBar is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7599409Z Services\ThemeSelectorService.cs(113,21): warning Uno0001: Windows.UI.ViewManagement.ApplicationViewTitleBar.ButtonBackgroundColor is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7603866Z Services\ThemeSelectorService.cs(118,25): warning Uno0001: Windows.UI.ViewManagement.ApplicationViewTitleBar.ForegroundColor is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7614949Z Services\ThemeSelectorService.cs(126,21): warning Uno0001: Windows.UI.ViewManagement.ApplicationViewTitleBar.BackgroundColor is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7621242Z Services\ThemeSelectorService.cs(128,21): warning Uno0001: Windows.UI.ViewManagement.ApplicationViewTitleBar.ButtonInactiveBackgroundColor is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7625920Z Services\ThemeSelectorService.cs(129,21): warning Uno0001: Windows.UI.ViewManagement.ApplicationViewTitleBar.ButtonInactiveForegroundColor is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
Here I use the theme selector to change theme color or change light or dark mode.\
Part of this is to customize the title bar. This is not supported by both Uno and UAP versions that do not support **Windows.UI.ViewManagement.ApplicationView**.\
This allows us to put this whole subroutine in a #if directive for NETFX_CORE.

```
/// <summary>
/// Sets the theme.
/// </summary>
/// <param name="theme">The theme.</param>
public void SetTheme(object theme)
{
    Theme = (ElementTheme)theme;
    SaveThemeInSetting((ElementTheme)Theme);

    if (Window.Current.Content is FrameworkElement frameworkElement)
    {
        if (frameworkElement.RequestedTheme == ElementTheme.Dark)
        {
            frameworkElement.RequestedTheme = ElementTheme.Light;
        }

        frameworkElement.RequestedTheme = (ElementTheme)Theme;
    }

    #if NETFX_CORE
    SetupTitlebar();
    #endif

    OnThemeChanged(null, (ElementTheme)Theme);
}

#if NETFX_CORE
/// <summary>
/// Setups the titlebar.
/// </summary>
private void SetupTitlebar()
{
    if (ApiInformation.IsTypePresent("Windows.UI.ViewManagement.ApplicationView"))
    {
        var coreTitleBar = CoreApplication.GetCurrentView().TitleBar;
        coreTitleBar.ExtendViewIntoTitleBar = true;

        var titleBar = ApplicationView.GetForCurrentView().TitleBar;

        if (titleBar != null)
        {
            titleBar.ButtonBackgroundColor = Colors.Transparent;

            if ((ElementTheme)Theme == ElementTheme.Dark)
            {
                titleBar.ButtonForegroundColor = Colors.White;
                titleBar.ForegroundColor = Colors.White;
            }
            else
            {
                titleBar.ButtonForegroundColor = Colors.Black;
                titleBar.ForegroundColor = Colors.Black;
            }

            titleBar.BackgroundColor = Colors.Black;

            titleBar.ButtonInactiveBackgroundColor = Colors.Transparent;
            titleBar.ButtonInactiveForegroundColor = Colors.LightGray;
        }
    }
}
#endif
```

Problem solved.

# MainView.CoreWindow.Dispatcher
```
2020-10-13T07:32:10.7684067Z Services\NavigationService.cs(376,19): warning Uno0001: Windows.ApplicationModel.Core.CoreApplicationView.CoreWindow is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
The problem here is that I use a dispatcher from UWP, namely **MainView.CoreWindow.Dispatcher**.\
Let's see if we have a Uno variant for this.\
And indeed Uno has an implementation for this. Heads other than UAP use **CoreDispatcher.Main** instead.

I changed the code from:
```
/// <summary>
/// clean back stack as an asynchronous operation.
/// </summary>
public async Task CleanBackStackAsync()
{
    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal,
    () =>
    {
        ((Frame)Frame).BackStack.Clear();
    });
}
```
To:
```
/// <summary>
/// clean back stack as an asynchronous operation.
/// </summary>
public async Task CleanBackStackAsync()
{
#if NETFX_CORE
    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
        CoreDispatcherPriority.Normal, 
        () => ((Frame)Frame).BackStack.Clear());
#else
    await CoreDispatcher.Main.RunAsync(
        CoreDispatcherPriority.Normal, 
        () => ((Frame)Frame).BackStack.Clear());
#endif
}
```

# SuspendingOperation
```
2020-10-13T07:32:10.7731263Z Application\BaseApplication.cs(330,28): warning Uno0001: Windows.UI.WebUI.SuspendingEventArgs.SuspendingOperation is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
This error indicates that **SuspendingOperation** is not supported by Uno.\
This is because a using statement under non-uap head has been misused.\
Instead of ```using Windows.ApplicationModel;``` I used ```using Windows.UI.WebUI;``` for the android and wasm head.\
After removing it, this error is gone.

# BackgroundTasks
```
2020-10-13T07:32:10.7737747Z Application\BaseApplication.cs(333,34): warning Uno0001: Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
This one is a little trickier because I use the [UWP BackgroundTask](https://docs.microsoft.com/en-us/windows/uwp/launch-resume/support-your-app-with-background-tasks) here which I'm not sure if is's already supported by the Uno platform.

Let's find out.

In the documentation I didn't came across anything about BackgroundTasks. However, on the [Uno Platform channel](https://discord.com/channels/372137812037730304/550416151172087808) on [Discord](https://discord.com) I found an alternative called [Shiny](https://github.com/shinyorg/shiny)

Maybe a nice alternative to the current implementation. But because of the focus I need to keep now, I'll make sure that this feature is only available for the UAP head for this moment. Later, I'm going to cover it in a separate chapter when I start on the actual implementation.

# Application.Exit()
```
2020-10-13T07:32:10.7766887Z ViewModels\ShellViewModelBase.cs(479,13): warning Uno0001: Windows.UI.Xaml.Application.Exit() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
This is the actual code:
```
/// <summary>
/// restart application as an asynchronous operation.
/// </summary>
public async Task RestartApplicationAsync()
{
    await BaseCommonServices.DialogService.ShowInformationAsync("Please restart the application.");
    Application.Current.Exit();
}
```
If I look at that code now, that **Application.Current.Exit** is a little redundant.\
I explicitly ask the users to restart the application themselves.\
In fact, in a web environment, the user will only have to refresh the web page.\
So this line can be removed.
```
/// <summary>
/// restart application as an asynchronous operation.
/// </summary>
public Task RestartApplicationAsync() =>
    BaseCommonServices.DialogService.ShowInformationAsync("Please restart the application.");
```
# NavigationCacheMode
```
2020-10-13T07:32:10.7969011Z Controls\Views\View.cs(44,35): warning Uno0001: Windows.UI.Xaml.Navigation.NavigationCacheMode.Required is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
At this moment (10/21/2020), NavigationCacheMode is not yet implemented.\
Tracking this [Github issue (4156)](https://github.com/unoplatform/uno/issues/4156) shows the progress on this matter. For now I will remove this line because I'm already caching the viewmodels in the dependency container.

# Geometry.Empty
```
2020-10-13T07:32:10.8504528Z Converters\ResourceConverters.cs(34,24): warning Uno0001: Windows.UI.Xaml.Media.Geometry.Empty is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
It seems that using Geometry.Empty is not supported.\
However, you can mimic this method by providing an empty geometric xml string.\
The code has changed from:
```
/// <summary>
/// Converts the specified value.
/// </summary>
/// <param name="value">The value.</param>
/// <param name="targetType">Type of the target.</param>
/// <param name="parameter">The parameter.</param>
/// <param name="language">The language.</param>
/// <returns>System.Object.</returns>
public object Convert(object value, Type targetType, object parameter, string language)
{
    if (parameter != null && parameter is string)
    {
        return (Geometry)XamlReader.Load(
                "<Geometry xmlns='http://schemas.microsoft.com/winfx/2006/xaml/presentation'>"
                + Application.Current.Resources[parameter] + "</Geometry>");
    }
    else
    {
        return Geometry.Empty;
    }
}
```
to:
```
/// <summary>
/// Converts the specified value.
/// </summary>
/// <param name="value">The value.</param>
/// <param name="targetType">Type of the target.</param>
/// <param name="parameter">The parameter.</param>
/// <param name="language">The language.</param>
/// <returns>System.Object.</returns>
public object Convert(object value, Type targetType, object parameter, string language)
{
    if (parameter != null && parameter is string)
    {
        return (Geometry)XamlReader.Load($"<Geometry xmlns='http://schemas.microsoft.com/winfx/2006/xaml/presentation'>{Application.Current.Resources[parameter]}</Geometry>");
    }
    else
    {
        return (Geometry)XamlReader.Load("<Geometry xmlns='http://schemas.microsoft.com/winfx/2006/xaml/presentation'></Geometry>");
    }
}
```

# ProcessLauncher 
In WPF and UWP applications I use a console control that uses the ProcessLauncher to start processes. This functionality is not available in mobile platforms. Maybe this will change later, but for now I'll only make it available for the UAP head. 

# RichTextBlock
An additional problem is that I use the RichTextBlock for the console, which is not yet supported by the Uno Platform.


# DecimalFormatter
```
2020-10-13T07:32:10.8508959Z Converters\DecimalFormatConverter.cs(25,36): warning Uno0001: Windows.Globalization.NumberFormatting.DecimalFormatter is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8513471Z Converters\DecimalFormatConverter.cs(29,17): warning Uno0001: Windows.Globalization.NumberFormatting.DecimalFormatter.FractionDigits is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8528287Z Converters\DecimalFormatConverter.cs(36,13): warning Uno0001: Windows.Globalization.NumberFormatting.DecimalFormatter.IntegerDigits is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
At this moment Uno doesn't support **DecimalFormatter** other than in the UAP head.\
Adding an ```#if NETFX_CORE``` directive solves this issue for now.\

# RandomAccessStream
```
2020-10-13T07:32:10.8130891Z Helpers\ImageHelper.cs(28,42): warning Uno0001: Windows.ApplicationModel.DataTransfer.StandardDataFormats.Bitmap is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8134754Z Helpers\ImageHelper.cs(30,43): warning Uno0001: Windows.ApplicationModel.DataTransfer.DataPackageView.GetBitmapAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8138540Z Helpers\ImageHelper.cs(34,51): warning Uno0001: Windows.Storage.Streams.RandomAccessStreamReference.OpenReadAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8142614Z Helpers\ImageHelper.cs(36,21): warning Uno0001: Windows.UI.Xaml.Media.Imaging.BitmapSource.SetSource(Windows.Storage.Streams.IRandomAccessStream) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8160635Z Helpers\ImageHelper.cs(54,79): warning Uno0001: Windows.ApplicationModel.DataTransfer.DataPackageView.GetBitmapAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8165960Z Helpers\ImageHelper.cs(56,48): warning Uno0001: Windows.Storage.Streams.RandomAccessStreamReference.OpenReadAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8171058Z Helpers\ImageHelper.cs(58,41): warning Uno0001: Windows.Graphics.Imaging.BitmapDecoder.CreateAsync(Windows.Storage.Streams.IRandomAccessStream) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8176276Z Helpers\ImageHelper.cs(59,48): warning Uno0001: Windows.Graphics.Imaging.BitmapDecoder.GetSoftwareBitmapAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8181080Z Helpers\ImageHelper.cs(61,46): warning Uno0001: Windows.Storage.Streams.InMemoryRandomAccessStream is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8185558Z Helpers\ImageHelper.cs(62,41): warning Uno0001: Windows.Graphics.Imaging.BitmapEncoder.CreateAsync(System.Guid, Windows.Storage.Streams.IRandomAccessStream) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8189736Z Helpers\ImageHelper.cs(62,67): warning Uno0001: Windows.Graphics.Imaging.BitmapEncoder.PngEncoderId is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8196809Z Helpers\ImageHelper.cs(63,21): warning Uno0001: Windows.Graphics.Imaging.BitmapEncoder.SetSoftwareBitmap(Windows.Graphics.Imaging.SoftwareBitmap) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8200501Z Helpers\ImageHelper.cs(67,31): warning Uno0001: Windows.Graphics.Imaging.BitmapEncoder.FlushAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8206576Z Helpers\ImageHelper.cs(74,39): warning Uno0001: Windows.Storage.Streams.InMemoryRandomAccessStream.Size is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8220445Z Helpers\ImageHelper.cs(75,27): warning Uno0001: Windows.Storage.Streams.InMemoryRandomAccessStream.ReadAsync(Windows.Storage.Streams.IBuffer, uint, Windows.Storage.Streams.InputStreamOptions) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8224578Z Helpers\ImageHelper.cs(75,75): warning Uno0001: Windows.Storage.Streams.InMemoryRandomAccessStream.Size is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8228627Z Helpers\ImageHelper.cs(75,94): warning Uno0001: Windows.Storage.Streams.InputStreamOptions.None is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8232858Z Helpers\ImageHelper.cs(124,17): warning Uno0001: Windows.UI.Xaml.Media.Imaging.BitmapSource.SetSource(Windows.Storage.Streams.IRandomAccessStream) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8236627Z Helpers\ImageHelper.cs(124,34): warning Uno0001: System.IO.Stream.AsRandomAccessStream() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8243027Z Helpers\ImageHelper.cs(140,30): warning Uno0001: Windows.Storage.StorageFile.GetFileFromApplicationUriAsync(System.Uri) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8247959Z Helpers\ImageHelper.cs(142,44): warning Uno0001: Windows.Storage.StorageFile.OpenSequentialReadAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8252286Z Helpers\ImageHelper.cs(144,34): warning Uno0001: Windows.Storage.Streams.IInputStream.AsStreamForRead() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
As you can see there are many more errors here. A persistent one, is an error caused by Uno because it does not yet support the **RandomAccessStream**.\
Also, the clipboard appears to only support text or uri's. Not images.\
These are only supported by the UAP head.\
That's why I refactored some functionality and moved these features to a Clipboard service.\
The more generic features I have moved to extension functions that also make more sense.

# ScrollIntoView
```
2020-10-13T07:32:10.8440535Z Extensions\ListViewExtensions.cs(143,17): warning Uno0001: Windows.UI.Xaml.Controls.ListViewBase.ScrollIntoView(object) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
By enclosing this line in a ```#if NETFX_CORE || __ANDROID__``` directive, the error is gone.
This means also that under a different head, ScrollIntoView will not be executed. 


# Storage
```
2020-10-13T07:32:10.8384449Z Extensions\SettingsStorageExtensions.cs(27,20): warning Uno0001: Windows.Storage.ApplicationData.RoamingStorageQuota is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8391586Z Extensions\SettingsStorageExtensions.cs(116,47): warning S4457: Split this method into two, one handling parameters check and the other handling the asynchronous code. [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8395765Z Extensions\SettingsStorageExtensions.cs(143,35): warning Uno0001: Windows.Storage.IStorageItem.IsOfType(Windows.Storage.StorageItemTypes) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8399611Z Extensions\SettingsStorageExtensions.cs(143,49): warning Uno0001: Windows.Storage.StorageItemTypes.File is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8403134Z Extensions\SettingsStorageExtensions.cs(162,58): warning Uno0001: Windows.Storage.StorageFile.OpenReadAsync() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8407161Z Extensions\SettingsStorageExtensions.cs(163,51): warning Uno0001: Windows.Storage.Streams.IRandomAccessStream.GetInputStreamAt(ulong) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8426148Z Extensions\SettingsStorageExtensions.cs(164,23): warning Uno0001: Windows.Storage.Streams.DataReader.LoadAsync(uint) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8430423Z Extensions\SettingsStorageExtensions.cs(164,46): warning Uno0001: Windows.Storage.Streams.IRandomAccessStream.Size is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
At this moment Uno doesn't support **ApplicationData.RoamingStorageQuota, StorageFile.OpenReadAsync or RandomAccessStreams** other than in the UAP head.\
Adding an ```#if NETFX_CORE``` directive solves this issue for now.\

# ApplicationLanguages
```
2020-10-13T07:32:10.8260926Z Functions\LocalizationFunctions.cs(42,13): warning Uno0001: Windows.Globalization.ApplicationLanguages.PrimaryLanguageOverride is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8266350Z Functions\LocalizationFunctions.cs(45,13): warning Uno0001: Windows.ApplicationModel.Resources.Core.ResourceContext.Reset() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8271835Z Functions\LocalizationFunctions.cs(45,13): warning Uno0001: Windows.ApplicationModel.Resources.Core.ResourceContext.GetForViewIndependentUse() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
At this moment Uno doesn't support **ApplicationLanguages** other than in the UAP head.\
Adding an ```#if NETFX_CORE``` directive solves this issue for now.\
Later I will add localization to our sample project to test this assumption.

# ResourceHelper
```
2020-10-13T07:32:10.8051415Z Helpers\ResourceHelper.cs(73,13): warning Uno0001: Windows.UI.Xaml.Media.Imaging.BitmapSource.SetSource(Windows.Storage.Streams.IRandomAccessStream) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8058531Z Helpers\ResourceHelper.cs(73,29): warning Uno0001: System.IO.Stream.AsRandomAccessStream() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
This feature is no longer used. The LoadEmbeddedImageResource function can be removed.

# KeyboardHelper
```
2020-10-13T07:32:10.7843372Z Helpers\KeyboardHelper.cs(24,25): warning Uno0001: Windows.UI.Core.CoreWindow.GetKeyState(Windows.System.VirtualKey) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
This feature is no longer used. This entire class can be removed.

# TitleBarHelper
```
2020-10-13T07:32:10.8024474Z Helpers\TitleBarHelper.cs(33,13): warning Uno0001: Windows.ApplicationModel.Core.CoreApplicationViewTitleBar.LayoutMetricsChanged is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8028389Z Helpers\TitleBarHelper.cs(34,53): warning Uno0001: Windows.ApplicationModel.Core.CoreApplicationViewTitleBar.SystemOverlayLeftInset is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.8032090Z Helpers\TitleBarHelper.cs(34,91): warning Uno0001: Windows.ApplicationModel.Core.CoreApplicationViewTitleBar.Height is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
This feature is no longer used. This entire class can be removed.

# VisualHelper
```
2020-10-13T07:32:10.7847379Z Helpers\VisualHelpers.cs(33,27): warning Uno0001: Windows.UI.Composition.Compositor.GetCommitBatch(Windows.UI.Composition.CompositionBatchTypes) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7851081Z Helpers\VisualHelpers.cs(35,47): warning Uno0001: Windows.UI.Composition.Compositor.CreateImplicitAnimationCollection() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7854766Z Helpers\VisualHelpers.cs(38,13): warning Uno0001: Windows.UI.Composition.CompositionObject.ImplicitAnimations is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7858516Z Helpers\VisualHelpers.cs(51,13): warning Uno0001: Windows.UI.Composition.KeyFrameAnimation.InsertExpressionKeyFrame(float, string) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7862318Z Helpers\VisualHelpers.cs(53,13): warning Uno0001: Windows.UI.Composition.CompositionAnimation.Target is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7867322Z Helpers\VisualHelpers.cs(75,13): warning Uno0001: Windows.UI.Composition.CompositionObject.ImplicitAnimations is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7872202Z Helpers\VisualHelpers.cs(75,36): warning Uno0001: Windows.UI.Composition.Compositor.CreateImplicitAnimationCollection() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7876577Z Helpers\VisualHelpers.cs(76,13): warning Uno0001: Windows.UI.Composition.ImplicitAnimationCollection.Add(string, Windows.UI.Composition.ICompositionAnimationBase) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7880802Z Helpers\VisualHelpers.cs(76,13): warning Uno0001: Windows.UI.Composition.CompositionObject.ImplicitAnimations is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7884369Z Helpers\VisualHelpers.cs(88,13): warning Uno0001: Windows.UI.Composition.CompositionAnimation.Target is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7891579Z Helpers\VisualHelpers.cs(119,25): warning Uno0001: Windows.UI.Composition.Compositor.CreateAnimationGroup() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7895379Z Helpers\VisualHelpers.cs(120,13): warning Uno0001: Windows.UI.Composition.CompositionAnimationGroup.Add(Windows.UI.Composition.CompositionAnimation) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7904668Z Helpers\VisualHelpers.cs(136,44): warning Uno0001: Windows.UI.Composition.Compositor.CreateImplicitAnimationCollection() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7908340Z Helpers\VisualHelpers.cs(139,13): warning Uno0001: Windows.UI.Composition.CompositionObject.ImplicitAnimations is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7917283Z Helpers\VisualHelpers.cs(151,13): warning Uno0001: Windows.UI.Composition.CompositionAnimation.Target is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7921316Z Helpers\VisualHelpers.cs(164,22): warning Uno0001: Windows.UI.Composition.Compositor.CreateVector3KeyFrameAnimation() is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7929149Z Helpers\VisualHelpers.cs(167,13): warning Uno0001: Windows.UI.Composition.CompositionAnimation.Target is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7932565Z Helpers\VisualHelpers.cs(184,17): warning Uno0001: Windows.UI.Composition.KeyFrameAnimation.DelayBehavior is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7936156Z Helpers\VisualHelpers.cs(184,43): warning Uno0001: Windows.UI.Composition.AnimationDelayBehavior.SetInitialValueBeforeDelay is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7939559Z Helpers\VisualHelpers.cs(189,13): warning Uno0001: Windows.UI.Composition.CompositionAnimation.Target is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7943458Z Helpers\VisualHelpers.cs(228,17): warning Uno0001: Windows.UI.Composition.KeyFrameAnimation.DelayBehavior is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7947395Z Helpers\VisualHelpers.cs(228,43): warning Uno0001: Windows.UI.Composition.AnimationDelayBehavior.SetInitialValueBeforeDelay is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
2020-10-13T07:32:10.7950838Z Helpers\VisualHelpers.cs(233,13): warning Uno0001: Windows.UI.Composition.CompositionAnimation.Target is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]

2020-10-13T07:32:10.8067372Z Helpers\NavigationAnimationHelpers.cs(45,13): warning Uno0001: Windows.UI.Xaml.Hosting.ElementCompositionPreview.SetImplicitHideAnimation(Windows.UI.Xaml.UIElement, Windows.UI.Composition.ICompositionAnimationBase) is not implemented in Uno [D:\a\1\s\src\ISynergy.Framework.UI\ISynergy.Framework.UI.csproj]
```
This functionality is now part of the XAML UI by default. This class, the related FrameAdapter and NavigationAnimationHelpers can be removed.