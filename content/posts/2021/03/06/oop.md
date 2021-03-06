+++ 
draft = false
date = 2021-03-06T10:02:53+01:00
title = "Object Oriented Programming (OOP)"
description = "What is it, and what is it used for?"
tags = ["OOP", "Object Oriented Programming", "C#", ".Net"]
categories = ["Software Development"]
series = ["General"]
+++
![](/images/oop.png)

# Object oriented programming (OOP)
One of the concepts within software development that is quite abstract, is object oriented programming.\
In my earlier years as a programmer, I had a lot of trouble wrapping my mind around this concept.\
Because I started with Visual Basic 6 and VBA and where you mainly program procedurally, I had trouble with this concept.\
You simply call one function or procedure after another and "weave" the code together. It worked pretty well but also had a lot of shortcomings.\
Especially if your application became larger than one class or module.\
In scripted languages such as VBA but also, for example Powershell, this is not a problem at all. 
And yet you will have used object-oriented elements without knowing.\
The string in which you place a value is an object. The use of the ```new()``` keyword is also a reference and instantiation of
an object.

## Objects
So what is an object? 

***An object is a standalone entity (subject) that we can talk about and think about and that has properties and/or that can do or execute stuff.***

The best way to illustrate this, is by means of an example.

Let's say you have a pen. Then this pen is an object.

![](/images/pen.jpg)

What can we say about this pen?\
The pen has a number of functions/methods that do something
- On/off button
- Some pens can also use different colors
- Writing (releasing ink on paper)

## Functions
If the procedure (the block of code) returns a result, then it's called a function. The result of this function can be used by other procedures. 

![](/images/ink-cartridge.png)

If you write this code, you have to give something back. Even if this is just a null value.\
The piece of code that uses this function (requests) can do something with this result, but it doesn't have to.\
In our case the function will return the ink level available in %.

```
public int InkLevel()
{
    \\\ returns ink level in %...
}

public void GetInkLevel()
{
    \\\ normal use of function:
    var level = InkLevel();

    \\\ calling a function without using it's result:
    \\\ however, it defeats the purpose of an function!
    InkLevel();
}
```

## Methods
If the piece of code doesn't give anything back, but simply executes the piece of code, then this is called a method.\
So functions and methods are quite identical only a method does not give anything back.

![](/images/ballpoint-tip.jpg)

With a method, you actually say: execute this piece of code and as soon as you are done I can run the next piece of code.

```
public void InjectInk()
{
    \\\ code for injecting ink on the ball of your pen...
}

public void ReleaseInk()
{
    \\\ code for releasing ink from the ball on paper...
}

public void WriteInk()
{
    \\\ runs first InjectInk...
    InjectInk();
    \\\ them ReleaseInk...
    ReleaseInk();
    \\\ rest of your code...
}
```

## Properties
The pen also has a number of properties.
- Color (all colors of the rainbow)
- Ink color (blue, black or red)
- Length
- Thickness
- Refillable (yes or no)

![](/images/pens.jpg)

These properties of the object can contain any value as preset.

For example: The property **Color** can contain any value as long it's in range of the System.Drawing.Color type. And the property **Refillable** can contain only a boolean value true or false.

So if we need to write a class of this pen, we need to add these properties to be able to set then at any time needed.

```
public class Pen
{
    public System.Drawing.Color Color { get; set; }
    public System.Drawing.Color InkColor { get; set; }
    public double Length { get; set; }
    public double Thickness { get; set; }
    public bool IsRefillable { get; set; }
}
```

Usually properties are publicly or at least partially publicly available. 
This also means that other objects, classes, or procedures can access and even modify this property.

## Variables
For example, if we don't want a property to be publicly available or adjustable, we can use a variable for this. This is a property that can often be used internally by the class for storing information.\
Variables can be defined at the class level so that all properties, functions and methods in that class have access to it or within a procedure that shields it from access outside this procedure.

In our example, a variable can be the ink level through which the pen ball may or may not be able to write.\
We can link this variable to a readonly property so that we can read how much ink is in this pen.

```
public class Pen
{
    private int _inkLevel;

    public int InkLevel 
    { 
        get { return _inkLevel; }; 
    }
}
```

In case of another example, like a car, this is the petrol level  that indicates the amount of available petrol.

![](/images/fuel-indicator.jpg)

Of course, this process is a little more complex, but hopefully you understand the idea.

## Encapsulation
This property (InkLevel) is readonly in a sense that I cannot directly adjust the ink level in any way.\
The only way is to perform a procedure that the manufacturer has devised and made available to turn the cap off and add a new ink cartridge or like in our case calling the ```ChangeInkLevel``` method.

```
public class Pen
{
    private int _inkLevel;

    public int InkLevel 
    { 
        get { return _inkLevel; }; 
    }

    public void ChangeInkLevel()
    {
        _inkLevel = 10;
    }
}
```

This principle is called encapsulation.

The value of this property is changed by means of a procedure.

A better example is the balance of your bank account. This property should never able to be modified from the outside. You can only request the balance. But actually changing the balance can only happen through another internal function such as "depositing money" or "withdrawing money".

The example with the ink cartridge of our pen immediately raises another problem because now at once our code example is no longer correct. Because with this action we have replaced an existing ink cartridge (which is actually another object) with another.

## Composition
Now it turns out that our pen is not one object, but consists of several other parts.\
Try twisting a pen apart and see how many parts you have now.\
In fact, we can assign and use our functions, methods, and properties for each component separate.\
So our previous example with the ink cartridge has its own properties and methods that our pen can use.

```
public class Pen
{
    public InkCartridge CurrentInkCartridge { get; set; }
    public System.Drawing.Color Color { get; set; }
    public double Length { get; set; }
    public double Thickness { get; set; }
    public bool IsRefillable { get; set; }

    public void ReplaceCartridge(InkCartridge newInkCartridge)
    {
        CurrentInkCartridge = newInkCartridge;
    }
}

public class InkCartridge
{
    private int _inkLevel;

    public int InkLevel 
    { 
        get { return _inkLevel; }; 
    }

    public System.Drawing.Color InkColor { get; set; }
}
```

This principle is called with composition, where an object can contain multiple objects.

## Inheritance
When I look in my pen tray I see all different pens (writing supplies) with each having different non-interchangable parts.\
Are these still the same pens?\
Actually, they aren't.

They are all different objects that do have similarities such as writing or that they have an indicator of the ink level. 
As an exception, my Parker pens don't have this indicator.

Do I have to redefine these objects and make 25 to 30 copies of my pen code? No, you shouldn't.

Inheritance allows us to inherit certain properties, methods, and functions from another object. So in this example, I would create a base class ```BaseWritingSupplies``` that has a number of properties that have all the pens in common. For example: color, ink color, and refillable.
But you can also inherit the functions and methods in this way. 

```
public abstract class BaseWritingSupplies
{
    public InkCartridge CurrentInkCartridge { get; set; }
    public System.Drawing.Color Color { get; set; }
    public double Length { get; set; }
    public double Thickness { get; set; }
    public bool IsRefillable { get; set; }

    public virtual void ReplaceCartridge(InkCartridge newInkCartridge)
    {
        CurrentInkCartridge = newInkCartridge;
    }
}

public class PenA : BaseWritingSupplies
{
}

public class PenB : BaseWritingSupplies
{
    public PenB()
    {
        IsRefillable = false;
    }

    public virtual void ReplaceCartridge(InkCartridge newInkCartridge)
    {
        throw new NotImplementedException("Ink cartridge cannot be replaced!");
    }
}
```

Another advantage is that I can overwrite the existing inherited methods or features with deployment-specific code that can be different for each pen.\
Here, **PenB** is a non-refillable pen.

## Conclusion
Hopefully, with this example, I've been able to clarify a little bit on what object-oriented programming is about.

Thank you for your time, and till next time.
