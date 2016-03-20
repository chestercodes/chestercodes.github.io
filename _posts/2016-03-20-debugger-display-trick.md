---
layout: post
title: Debugger display trick
excerpt: Generating test after data
tags: 
- c-sharp
- testing
- code-generation
---

## Debugger display test data generating trick ##

In my day to day it's not always possible to be able to properly write code in a TDD manner. The lack of existing code coverage, tight coupling of components and time constraints mean that a test after strategy is sometimes required.

Recently I was maintaining some code that serialised XML into C# objects to be loaded into a database. The C# objects were fairly simple, a couple of string properties detailing the values and table names. Each XML file produced 10-100s of objects and I wanted a way to quickly get test data to add to tests that I was writing.

I wanted to get the code to produce these objects without writing them out by hand. I found a trick to doing this using the visual studio locals window and the DebuggerDisplay attribute.

Debugger display allows you to change how your object is seen whilst debugging. If you have a collection of a type then instead of just seeing the type name you can quickly see a summary of the objects in the collection. This is done by using the attribute and specifying what properties of the object you want to see.

To take advantage of this I added a `string ToDisplay {get; set;}` property to the class of the objects being produced. This property holds a string representation of the constructor needed to make this object.

An example can be seen here. 

``` csharp
[DebuggerDisplay("{ToDisplay}")]
public class SimpleClass
{
	public string SomeString { get; set; }
	public bool SomeBool { get; set; }
	public int SomeInt { get; set; }
	public string ToDisplay => PrintToDisplay();
	private string PrintToDisplay()
	{
		return $"new SimpleClass(){{ " +
		       $"{nameof(SomeString)} = |{SomeString}|, " +
		       $"{nameof(SomeBool)} = {SomeBool.ToString().ToLower()}, " +
		       $"{nameof(SomeInt)} = {SomeInt}, " +
		       $"}}";
	}
}
```

Instead of the `"` I used a `|` in an attempt to escape the string. This will obviously break if the string contains this character.

To get the values whilst debugging you can select the rows, right click and Copy Value. 

This will give the string contents and you can delete the first and last quote and then replace the `|` with `"` to get the C# code.

This trick saved me a lot of time and may be of use to you. It would also be possible to write the `ToDisplay` property to a file and this would mean that the string's wouldn't need to be un-escaped.