+++ 
draft = false
date = 2021-03-07T10:50:39+01:00
title = "Dependency Injection"
description = "Constructor or Property Injection?"
tags = ["DI", "Dependency Injection", "Constructor", "Property", "C#", ".Net"]
categories = ["Software Development"]
series = ["General"]
+++
![](/images/Injection.png)

# Dependency Injection

A term that is widely used in software development is Dependency Injection.\
But what is it, what is it for and better yet what is its usefulness?

In object-oriented programming, we use human understandable objects to be able to use them within other objects or classes.\
To use an object within a class or other object, it must first be instantiated.\
It creates a new object and sets memory for this object.

We often use the ```new()``` keyword to do this.\
We can also often provide additional parameters that can be used to set certain settings when creating.\
This is a fairly general method of creating new objects but also has a number of major disadvantages.

First of all, and I also think the biggest problem, is that the class (dependency) that the object (dependee) needs to create should have some knowledge about that object.\
This object is actually static in the sense that from that moment on it's an essential part of the calling class.
You can call it a hard dependency.

You often don't want this and certainly not when the project gets bigger and more complex.\
Refactoring and testing also becomes more difficult, because you want each class to use the single responsibility principle.\
The class may only contain code for which it is responsible.

This is where dependency injection comes in. This makes it possible to refer and use loosly coupled objects often in the form of an interface.
These interfaces can be adapted to any implementation required. In tests these interfaces are mocked (fakes).

This is also desirable during testing, because you just want to test this class and nothing more.\
If you had the implementation in the class directly or indirectly, the test would also bubble up to these external classes so that your (unit) test would no longer be independent. The results of the tests could also be influenced by that external class.

Now there are 2 forms of dependency injection, one of which should be avoided as many as possible.

## Constructor Injection
With the **Constructor Injection**, the dependencies are given to the constructor as parameters.\
By connecting them to a local variable, this dependency can be used within this class. 

```
public class Pen
{
    private readonly IInkCartridge _inkCartridge;

    /// the dependency injector will inject the real implementation of this interface.
    public Pen(IInkCartridge inkCartridge)
    {
        _inkCartridge = inkCartridge;
    }
}
```

## Property Injection
With a **Property Injection**, properties are created that refer to the dependency. As soon as the class is instanceed, the dependency injector ensures that the properties are filled with the correct value. As you can see here, the constructor of the class is also empty.

```
public class Pen
{
    private readonly IInkCartridge _inkCartridge;

    /// the dependency injector will inject the real implementation of this interface.
    public IInkCartridge InkCartridge
    {
        get { return _inkCartridge; }
        set { _inkCartridge = value; }
    }

    /// the constructor has no parameters.
    public Pen()
    {
    }
}
```

## Okay, now that we know this, which one should I use?

**Always use the **Constructor Injection**!**

**Constructor Injection** has a large number of advantages over those of the **Property Injection**.\
**Constructor Injection** ensures that the dependency is immutable.\
It can't just be changed, which is often not the case with a property.\
Because the dependency variable is also often readonly and can only be changed by the constructor, you do not run the risk that it will be changed somewhere in the process, which is simply possible with a property.

More importantly, in constructor dependencies these variables are set when instantizing the class.\
If the dependency is not available or null, the class will fail immediately at the beginning.\
Otherwise the class knows now that all dependencies are loaded and validated and ready to run.

With **Property Injection**, the class can never know whether all dependencies have been resolved or even are null.\
Do you have to look at every property - with, for example, an if statement - whether it has a valid reference?\
And what should happen if it's not there?\
In fact, you're going to throw an exception here to be kept informed that something went wrong. Not a very elegant solution.

```
public class Pen
{
    private readonly IInkCartridge _inkCartridge;

    /// the dependency injector will inject the real implementation of this interface.
    public IInkCartridge InkCartridge
    {
        get { return _inkCartridge; }
        set { _inkCartridge = value; }
    }

    /// the constructor has no parameters.
    public Pen()
    {
    }

    public void ReplaceInkCartridge()
    {
        if(_inkCartridge is null)
            throw new ArgumentNullException("Ink cartridge not found!");

        _inkCartridge.Replace();
    }
}
```

But also as a programmer, **Constructor Injection** ensures you that you see exactly which dependencies are required for this contructor.\
Also, one can never use a constructor where one of the dependencies has been forgotten or not set.


> # "Yes, but, I don't want to have a constructor with dozens of parameters."

This excuse is widely used because there is a groing annoyance as soon the number of parameters in the constructor has increased.\
Whereas actually the real problem probably is that your class might violate the single responsibility principle.\
Apparently, your class is doing more than he should!

You could split your class into smaller functional parts to solve this problem.\
This also helps to test the classes.

In my viewmodels I always use 6 interfaces (IContext, IDialogservice, INavigationService, ILoggerFactory and a few more) that I have encapsulated in another interface and are handled very neatly via dependecy injection. Now i've converted 6 parameters to one.

So this argument should never be used against the use of **Constructor Injection**.

> # "What if I use inheritance and don't want to change my base class?"

Yes, here you can use **Property Injection**, but why should you?\
Laziness shouldn't be the reason.
Somehow you're going to pay the bill for this way out with extra work.\

> # "What if I have an object that depends on another object and then on mine?"

This is the only reason why I would opt in for **Property Injection** because I can't change the base object.\
These are often objects that can only work with a parameterless constructor (for example, XAML converters).\
There is no way to adjust these classes here.

You could use the ServiceLocator pattern here, but especially here, **Property Injection** is a valid and even better alternative.