+++
categories = ["Software Development"]
date = "2020-10-01T20:18:59+02:00"
description = "Starting the migration of my UWP library"
draft = false
series = ["Uno Platform"]
tags = ["Uno Platform", "UWP", "C#", ".Net", "Visual Studio", "Microsoft", "I-Synergy"]
title = "Uno Platform - Part 3"

+++
Hi, today I'm going to migrate the library to the Uno Platform.

The previous post we had seen that the sample application gave all kind of errors and was no longer buildable. The reason for this was because the sample depended heavily on the UWP library that could no longer be used.

[Uno's advice is to create a new project in addition to the existing project](https://platform.uno/docs/articles/howto-migrate-existing-code.html), where we will all transfer the code.

Again, I'm going to try to show you what I'm doing step by step.\
If you haven't read the [previous post](https://ismail.hassani.eu/posts/2020/09/16/unoplatform_part1/), do it first.

# Create new library
![](/images/2020-09-30.png)
> Right mouse click the folder and add a new project.

![](/images/2020-09-30-1.png)
> Enter "uno" in the search bar and select "Cross-Platform Library (Uno Platform)" and then press "Next".

![](/images/2020-09-30-2.png)
> First select the right directory location where you want to save this project.\
Enter the correct “Project name” (e.g. ISynergy.Framework.UI) and click on “Create”.

After creating the project, we can remove the automatically created class.\
What we ended up with is a pristine project for our new UI library.

Next step we have to take is to check if our new csproj is up to date and has all required elements.\
By default the target frameworks are set to UWP 16299, .Net Standard 2.0, IOS, MacOS (Xamarin) and Mono Android (version 9 and 10). Like our previous sample, we don't have any Apple hardware to support compilation of IOS and MacOS. Therefore I'll remove these targets.\
Also I'll update the UWP version to uap10.0.17763. This is because the Microsoft.UI.Xaml 2.4 NuGet package requires your project to have TargetPlatformVersion >= 10.0.18362.0 when building.

```
<?xml version="1.0" encoding="utf-8"?> 
<Project Sdk="MSBuild.Sdk.Extras/2.0.54">   
<PropertyGroup>     
<TargetFrameworks>uap10.0.16299;netstandard2.0;xamarinios10;xamarinmac20;MonoAndroid90;monoandroid10.0</TargetFrameworks>     

<!-- Ensures the .xr.xml files are generated in a proper layout folder -->     
<GenerateLibraryLayout>true</GenerateLibraryLayout>   
</PropertyGroup>   
<ItemGroup>     
<PackageReference Include="Uno.UI" Version="3.0.12" />   
</ItemGroup>   
<ItemGroup>     
<Page Include="**\*.xaml" Exclude="bin\**\*.xaml;obj\**\*.xaml" />     
<Compile Update="**\*.xaml.cs">       
<DependentUpon>%(Filename)</DependentUpon>     
</Compile>   
</ItemGroup>   
<ItemGroup>     
<UpToDateCheckInput Include="**\*.xaml" Exclude="bin\**\*.xaml;obj\**\*.xaml" />
</ItemGroup> 
</Project></pre>
```

After changing the frameworks, adding imports and my extra magic, this file should look like this.

```
<?xml version="1.0" encoding="utf-8"?> 
<Project Sdk="MSBuild.Sdk.Extras/2.0.54">   
<Import Project="..\..\project.props" />   
<PropertyGroup>     
<TargetFrameworks>netstandard2.0;uap10.0.17763;MonoAndroid90;monoandroid10.0</TargetFrameworks>     

<!-- Ensures the .xr.xml files are generated in a proper layout folder -->     
<GenerateLibraryLayout>true</GenerateLibraryLayout>     
<!-- SonarQube needs this -->     
<ProjectGuid>{FE7A8E6E-10A1-4C31-90D5-73EBB068E570}</ProjectGuid>
</PropertyGroup>   
<PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Debug|AnyCPU'">
<OutputPath>..\..\output\ISynergy.Framework.UI\debug</OutputPath>
</PropertyGroup>
<PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Release|AnyCPU'">
<OutputPath>..\..\output\ISynergy.Framework.UI\release</OutputPath>
<NoWarn>1701;1702;1591</NoWarn>
</PropertyGroup>
<ItemGroup>
<PackageReference Include="Uno.UI" Version="3.0.12" />
</ItemGroup>
<ItemGroup>
<Page Include="**\*.xaml" Exclude="bin\**\*.xaml;obj\**\*.xaml" />
<Compile Update="**\*.xaml.cs">
<DependentUpon>%(Filename)</DependentUpon>
</Compile>
</ItemGroup>
<ItemGroup>
<UpToDateCheckInput Include="**\*.xaml" Exclude="bin\**\*.xaml;obj\**\*.xaml" />
</ItemGroup>
<ItemGroup>
<ProjectReference Include="..\ISynergy.Framework.Mvvm\ISynergy.Framework.Mvvm.csproj" />
</ItemGroup>
</Project></pre>
```

Try to build this project after these changes to check if everything works fine.

# Migrate dependencies
The next step now is to move over the nuget and project references from the old library.

In our situation we want to add a project reference to the ISynergy.Framework.Mvvm which indirectly adds a reference to ISynergy.Framework.Core.

But wait! What just happened?

![](/images/2020-09-30-3.png)

Restarting Visual Studio didn't solve this issue, so what the heck is happening here?

A quick search leads me to a post from [Jérôme Laban](https://jaylee.org/) on [Developers Community of visualstudio.com](https://developercommunity.visualstudio.com/content/problem/1170010/missing-value-for-targetplatformwinmdlocation-prop.html).

It suggests that multi-target frameworks should not start with the uap value, which is now the case. When I click through to this [link](https://github.com/novotnyllc/MSBuildSdkExtras) in the post, I also notice here that they first use the "net46" property and then the "uap10.x.xxxxx".

If I change this line in my csproj file it should look like:

![](/images/2020-10-01-7.png)

Don't forget to restart Visual Studio after this change.\
But now it's working again and we can add the required project references. 👌

Ok, next thing is to add the remaining references.\
I have the currently following references:
- [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json)
- [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging)
- [Microsoft.Toolkit.Uwp.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.UI.Controls)
- [Microsoft.UI.Xaml](https://www.nuget.org/packages/Microsoft.UI.Xaml)
- [Microsoft.Xaml.Behaviors.Uwp.Managed](https://www.nuget.org/packages/Microsoft.Xaml.Behaviors.Uwp.Managed)

Some (the extensions) can installed just fine in our new library. For the others I may have to use the Uno Platform versions. The Microsoft.UI.Xaml will be replaced by [Uno.UI](https://www.nuget.org/packages/Uno.UI) that's already references in this project and [Uno.Core](https://www.nuget.org/packages/Uno.Core) that is added extra.

## Microsoft.Toolkit.Uwp.UI.Controls
This nuget package should be replaced by [Uno.Microsoft.Toolkit.Uwp.UI.Controls](https://www.nuget.org/packages/Uno.Microsoft.Toolkit.Uwp.UI.Controls)

## Microsoft.Xaml.Behaviors.Uwp.Managed
This nuget package should be replaced by [Uno.Microsoft.Xaml.Behaviors.Uwp.Managed](https://www.nuget.org/packages/Uno.Microsoft.Xaml.Behaviors.Uwp.Managed)

After the changes the csproj file should look a lot like this</p>

```
<?xml version="1.0" encoding="utf-8"?>
<Project Sdk="MSBuild.Sdk.Extras/2.0.54">
<Import Project="..\..\project.props" />
<PropertyGroup>
<TargetFrameworks>netstandard2.0;uap10.0.17763;MonoAndroid90;monoandroid10.0</TargetFrameworks>
<!-- Ensures the .xr.xml files are generated in a proper layout folder -->
<GenerateLibraryLayout>true</GenerateLibraryLayout>
<!-- SonarQube needs this -->
<ProjectGuid>{FE7A8E6E-10A1-4C31-90D5-73EBB068E570}</ProjectGuid>
</PropertyGroup>
<PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Debug|AnyCPU'">
<OutputPath>..\..\output\ISynergy.Framework.UI\debug</OutputPath>
</PropertyGroup>
<PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Release|AnyCPU'">
<OutputPath>..\..\output\ISynergy.Framework.UI\release</OutputPath>
<NoWarn>1701;1702;1591</NoWarn>
</PropertyGroup>
<ItemGroup>
<PackageReference Include="Microsoft.NETCore.UniversalWindowsPlatform">
<!--  If, in the same solution, you are referencing a project that uses https://github.com/onovotny/MSBuildSdkExtras, you need to make sure that the version provided here matches https://github.com/onovotny/MSBuildSdkExtras/blob/master/Source/MSBuild.Sdk.Extras/DefaultItems/ImplicitPackages.targets#L11. This is not an issue when libraries are referenced through nuget packages. See https://github.com/unoplatform/uno/issues/446 for more details. -->
<Version>6.2.10</Version>
</PackageReference>
<PackageReference Include="Microsoft.Extensions.Configuration.Json" Version="3.1.8" />
<PackageReference Include="Microsoft.Extensions.Logging" Version="3.1.8" />     <PackageReference Include="Uno.Core" Version="2.0.0" />
<PackageReference Include="Uno.Microsoft.Toolkit.Uwp.UI.Controls" Version="6.1.0-build.191.gc988bdd4ff" />
<PackageReference Include="Uno.Microsoft.Xaml.Behaviors.Uwp.Managed" Version="2.0.2-uno.76" />
<PackageReference Include="Uno.UI" Version="3.0.17" />
</ItemGroup>
<ItemGroup>
<Page Include="**\*.xaml" Exclude="bin\**\*.xaml;obj\**\*.xaml" />
<Compile Update="**\*.xaml.cs">
<DependentUpon>%(Filename)</DependentUpon>
</Compile>
</ItemGroup>
<ItemGroup>
<UpToDateCheckInput Include="**\*.xaml" Exclude="bin\**\*.xaml;obj\**\*.xaml" />
</ItemGroup>
<ItemGroup>
<ProjectReference Include="..\ISynergy.Framework.Mvvm\ISynergy.Framework.Mvvm.csproj" />
</ItemGroup>
</Project></pre>
```

Check if the new project is still building to go to the next step.

# Migrate code
Now we have the right references in place we can start moving code from the old library to the new one. The easiest way is to just copy the content of that project to the new location. With this method we keep a backup of our old code.

![](/images/2020-10-01.png)
> All our code is now copied to our new library.

Maybe it's wishful thinking, but lets try to rebuild our project to see what's going to pop up.

![](/images/2020-10-01-1.png)

Hmm, we got 2 distinct errors.

- XamlCompiler error WMC0001: Unknown type 'ColorPaletteResources' in XML namespace 'http://schemas.microsoft.com/winfx/2006/xaml/presentation'
- 'DependencyObjectCollection<>' is an ambiguous reference between 'ISynergy.Framework.UI.Collections.DependencyObjectCollection' and 'Windows.UI.Xaml.DependencyObjectCollection'
- error CS0123: No overload for 'Control_DataContextChanged' matches delegate 'TypedEventHandler
- error CS0103: The name 'ApplicationViewScaling' does not exist in the current context
- error CS0103: The name 'ProcessLauncher' does not exist in the current context
- error CS0246: The type or namespace name 'ProcessLauncherOptions' could not be found (are you missing a using directive or an assembly reference?)

The first error seems somehow more difficult to solve than the second one. Lets try to fix the second one first and rebuild.

### 'DependencyObjectCollection<>' is an ambiguous reference between 'ISynergy.Framework.UI.Collections.DependencyObjectCollection' and 'Windows.UI.Xaml.DependencyObjectCollection'
It seems that the using directive of Windows.UI.Xaml is causing this collision.\
Removing it should do the trick.

![](/images/2020-10-01-2.png)
> Before...

![](/images/2020-10-01-3.png)
> After.

After rebuild this error is gone.

### XamlCompiler error WMC0001: Unknown type 'ColorPaletteResources' in XML namespace 'http://schemas.microsoft.com/winfx/2006/xaml/presentation'
The other one is somehow tricky because during my conversation with [Ian Bebbington](https://ian.bebbs.co.uk/) a couple of weeks ago, he mentioned that dynamic theme switching was not yet available for the Uno Platform.

The files from where the errors are originating, are theme files which enable theme color switching in the UWP application. This issue happens because this class is introduced in Windows version 10.0.0.17763.0.\
The solution here is simply update your minimum target version to version uap10.0.17763 (1809).\
Let's not forget to update the sample minimum version too.

### Error CS0123: No overload for 'Control_DataContextChanged' matches delegate 'TypedEventHandler
![](/images/2020-10-01-4.png)

This error can be solved by using the #if directive to select the correct namespace.

```
/// <summary>         
/// Controls the data context changed.         
/// </summary>         
/// <param name="sender">The sender.</param>         
/// <param name="args">The <see cref="DataContextChangedEventArgs"/> instance containing the event data.</param>         
private void Control_DataContextChanged(FrameworkElement sender, DataContextChangedEventArgs args) => 
  Setup();
```

is changed to...

```
  /// <summary>         
  /// Controls the data context changed.         
  /// </summary>         
  /// <param name="sender">The sender.</param>         
  /// <param name="args">The <see cref="DataContextChangedEventArgs"/> instance containing the event data.</param> 
#if NETFX_CORE
  private void Control_DataContextChanged(FrameworkElement sender, DataContextChangedEventArgs args) => 
    Setup(); 
#else         
  private void Control_DataContextChanged(DependencyObject sender, DataContextChangedEventArgs args) => 
    Setup(); 
#endif
```

### Error CS0103: The name 'ApplicationViewScaling' does not exist in the current context, Error CS0103: The name 'ProcessLauncher' does not exist in the current context and Error CS0246: The type or namespace name 'ProcessLauncherOptions' could not be found (are you missing a using directive or an assembly reference?)
All names are not available for any head other then UWP. By using #if directives we can disable this line if it's not the UWP head.

This file contains more items that are UWP only, so check the source code to see the changes.\
Let's rebuild this project and see what happens.

![](/images/2020-10-01-5.png)

Nice! except the warnings that some functions or methods are not supported by Uno, the project has build successfully.

# Reference new library to sample
It is probably useful to first remove the old library from the solution.\
As a result, we are no longer disturbed by errors in this project.\
Later, if everything works well, we can remove the entire project directory.

![](/images/2020-10-01-6.png)

Now it's time to move all files (Assets, Views and App) to the Sample's shared library.\
After this, let's rebuild this sample project (UWP) to see if all errors have been fixed.

# Nested dependencies
In .Net Core I am used to the fact that nested references do not need to be added separately. However, in UWP this doesn't seem to work. Here you need to add all the underlying references of your library. Otherwise you get stuck with namespace exception like I have now.

This means that I have to add the following references manually to the sample project.
- ISynergy.Framework.Core
- ISynergy.Framework.Mvvm

# More errors...sigh!
hmm, even more errors…\
And these have to do with the NavigationView that I use from the Microsoft.UI.Xaml library. So I have to add this separately to the UWP sample.

That solved all the "Controls" errors, but there is one annoying one left I have to dive into:
- error CS5001: Program does not contain a static 'Main' method suitable for an entry point

![](/images/mental.png)
> Why, why, why!!!

Let first clean up the solution and remove all bin and obj directories in the sample project.\
Sadly this didn't help.

After comparing the old version, the solution was somehow simple afterwards.

Right click on the App.xaml file and select properties, then make sure build action is set to **ApplicationDefinition**.\
Then rebuild project and everything should be ok.

# Conclusion
I have to say that the migration now has been much smoother than previous attempts.\
Of course this is not a standard "Hello World" application. The more complexity is added, the more exceptions you will see.

For now we've focused mainly on the UWP head of Uno and we didn't run the UWP sample. So there may also be more errors in the future.

However, one thing I noticed, is that if I did a rebuild too quick after another, that an error message occurred during the build in the Android head. It looks like the files are still locked from the previous session and could not be overwritten. Waiting a minute or 2 seems to solve this issue. Anyway, it looks like a Xamarin bug.

```
3>C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\MSBuild\Xamarin\Android\Xamarin.Android.Common.targets(2829,2): error XARDF7024: System.IO.IOException: The directory is not empty.
3>C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\MSBuild\Xamarin\Android\Xamarin.Android.Common.targets(2829,2): error XARDF7024: 
3>C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\MSBuild\Xamarin\Android\Xamarin.Android.Common.targets(2829,2): error XARDF7024:    at System.IO.__Error.WinIOError(Int32 errorCode, String maybeFullPath)
3>C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\MSBuild\Xamarin\Android\Xamarin.Android.Common.targets(2829,2): error XARDF7024:    at System.IO.Directory.DeleteHelper(String fullPath, String userPath, Boolean recursive, Boolean throwOnTopLevelDirectoryNotFound, WIN32_FIND_DATA&amp; data)
3>C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\MSBuild\Xamarin\Android\Xamarin.Android.Common.targets(2829,2): error XARDF7024:    at System.IO.Directory.Delete(String fullPath, String userPath, Boolean recursive, Boolean checkHost)
3>C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\MSBuild\Xamarin\Android\Xamarin.Android.Common.targets(2829,2): error XARDF7024:    at Xamarin.Android.Tasks.RemoveDirFixed.RunTask()
3>Done building project "ISynergy.Framework.UI.csproj" -- FAILED.
```

My experience with this migration went surprisingly smooth. Very promising.\
Next time I'll start running the application to see if it runs, and if not, how to solve those problems.

For now, have a good night you all, stay healthy and till next time.