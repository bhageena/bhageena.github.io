---
layout: post
title: New Features in C Sharp 7
description: ""
categories: blog
comments: true
tags: "CSharp .Net  NewReleases Improvements"
excerpt: "The release of [C# 6.0](/blog/what's-new-in-C-6/) in July 2015, brought many long awaited features in C# namely - Exception filters, String interpolation, Dictionary initializer, Await in catch/finally blocks and many more. Now recently released version 7.0 introduces a handful of new features and brings focus on data consumption, code simplification and performance like never before. Let's take a look at what's in store."
share: true
readtime: true
---


The release of [C# 6.0](/blog/what's-new-in-C-6/) in July 2015, brought many long awaited features in C# namely - Exception filters, String interpolation, Dictionary initializer, Await in catch/finally blocks and many more. Now recently released version 7.0 introduces a handful of new features and brings focus on data consumption, code simplification and performance like never before. Let's take a look at what's in store...

* TOC
{:toc}

## Out variables

In earlier versions of C#, using out parameters isn’t as fluid as we’d like. Before you can call a method with out parameters you first have to declare variables to pass to it. Since you typically aren’t initializing these variables (they are going to be overwritten by the method after all), you also cannot use var to declare them, but need to specify the full type:

When calling methods with out parameters now you have the ability to declare a variable right at the point where it is passed as an out argument i.e. variables can be declared inline:

```csharp
if (int.TryParse ("123", out int result))   // result has been declared inline
	Console.WriteLine(result);

// Note that the newly introduced variable is still in scope:

Console.WriteLine(result);

// Out parameters can be implicitly typed (var):

int.TryParse ("234", out var result2);
Console.WriteLine(result);
```

If you don't need the value of an out parameter, you can discard it with an underscore

```csharp
if (int.TryParse (numberString, out _))   // Discard out argument
		Console.WriteLine("Valid number");
	else
		Console.WriteLine("Invalid number");
```

You can use the underscore multiple times in a single method call:

```csharp
DeepThought(out int interesting, out _, out _, out _);
	Console.WriteLine($"The answer to life is {interesting}");

void DeepThought(out int p1, out string p2, out bool p3, out char p4)
{
	p1 = 42;
	p2 = "fourty two";
	p3 = true;
	p4 = 'x';
}
```

## Pattern matching

Pattern matching is another feather in C#s cap, which helps in increasing the brevity and terseness of the language, we can now test for type and introduce a variable at the same time:

```csharp
object enigma = "What is life";

if (foo is string s)
	Console.WriteLine(s.Length);
```
We can also use type in the switch statements:

```csharp
void Main()
{
	TakeControl (new TextBox());
	TakeControl (new ComboBox());
	TakeControl (null);
	TakeControl (new MonthCalendar());
}

void TakeControl (Control control)
{
	switch (control)
	{
		case TextBox t:              // We can switch on *type*
			Console.WriteLine(t.Multiline);
			break;

		case ComboBox c when c.DropDownStyle == ComboBoxStyle.DropDown:
			Console.WriteLine(c.Items);
			break;

		case ComboBox c:             // The ordering of case clauses now matters!
			Console.WriteLine(c.SelectedItem);
			break;

		case null:
			Console.WriteLine("Null");
			break;

		default:
			Console.WriteLine("Unknown");
			break;
	}
}
```

## Tuples

Working with Tuples has become more easier than ever in C# 7, they can be created as easily:

```csharp
var tuple = ("fourtytwo", 42);
Console.WriteLine(tuple);
```
We can do so with explicit typing:
```csharp
(string, int) tuple2 = tuple;
```
We can even name the fields!
```csharp
var namedTuple = (word:"fourtytwo", number:42);
```

Named tuples are compiled the same underneath (Item1, Item2):

Console.WriteLine(namedTuple);

But with compiler trickery, we can refer to the names in source code:
```csharp
Console.WriteLine(namedTuple.number);
Console.WriteLine(namedTuple.word);
```
Tuples rely on a new type called System.ValueTuple, in the NuGet package of the same name.
LINQPad includes this automatically, so you don't have to import anything.

## Deconstruction
Now we have custom deconstruction in C#,  You can create your own by writing a method called "Deconstruct".

```csharp
void Main()
{
	var point = new Point (3, 4);
	var (x, y) = point;

	Console.WriteLine(x);
	Console.WriteLine(y);
}

class Point
{
    public int X { get; }
    public int Y { get; }

    public Point (int x, int y) { X = x; Y = y; }
    public void Deconstruct (out int x, out int y) { x = X; y = Y; }
}
```
Deconstruction also applies to the Tuples.

```csharp
void Main()
{
	// We can "deconstruct" a tuple into individual variables as follows:

	(string s, DateTime dt) = Foo();

	Console.WriteLine(s);
	Console.WriteLine(dt);

	// If the variables already exist, just omit the type names:

	(s, dt) = Foo();

	// You can implicitly type the variables if desired:

	var (s2, dt2) = Foo();

	Console.WriteLine(s2);
	Console.WriteLine(dt2);

	// You can discard a tuple member with an underscore:

	var (s3, _) = Foo();
	Console.WriteLine(s3);
}

(string, DateTime) Foo() => ("Now", DateTime.Now);
```



## Local functions

Now local functions cut some verbosity out of the C#, You can now declare a function within a function and it also works with iterators:
```csharp
void Main()
{
	Console.WriteLine(Fibonacci (10));
}

public int Fibonacci (int x)
{
	return Fib (x).current;

	(int current, int previous) Fib (int i)
	{
		if (i == 0) return (1, 0);
		var (p, pp) = Fib (i - 1);
		return (p + pp, p);
	}
}

```

## Digit separators
You can use underscores within numeric literals to make them more readable:
```csharp
int x = 12_000;
Console.WriteLine(x);
```

## Binary literals
The '0b' prefix now denotes a binary literal

```csharp
var b = 0b1010_1011_1100_1101_1110_1111;
Console.WriteLine(b);

```

## Ref returns and locals
Ref returns/locals are now possible for advanced scenarios

```csharp
void Main()
{
	int[] array = { 1, 15, -39, 0, 7, 14, -12 };
	ref int place = ref Find (7, array); // aliases 7's place in the array
	place = 9; // replaces 7 with 9 in the array
	Console.WriteLine (array [4]); // prints 9
}

public ref int Find (int number, int[] numbers)
{
	for (int i = 0; i < numbers.Length; i++)
	{
		if (numbers [i] == number)
		{
			return ref numbers [i]; // return the storage location, not the value
		}
	}
	throw new IndexOutOfRangeException ($"{nameof (number)} not found");
}
```

## Generalized async return types
C# 7.0 introduces the ability to define custom return types on async methods. The key requirement is any type that has an accessible `GetAwaiter` method. The object returned by the `GetAwaiter` method must implement the `System.Runtime.CompilerServices.ICriticalNotifyCompletion` interface. It is designed for the very scenario when the return value might be known immediately – cached from a previous invocation for example.  We can use this type as below in  `GetDirectorySize()` implementation to return a `ValueTask<long>` of `Task<long>`.  

```csharp
public async ValueTask<long> GetDirectorySize<T>(string path, string searchPattern)
{
    if (!Directory.EnumerateFileSystemEntries(path, searchPattern).Any())
        return 0;
    else
        return await Task.Run<long>(()=> Directory.GetFiles(path, searchPattern,
            SearchOption.AllDirectories).Sum(t => (new FileInfo(t).Length)));
}
```

## Expression bodied constructors and finalizers

Expression bodied constructors and finalizers allow use of expressions in constructors and finalizers. Now instead of declaring body, insert `=>` followed by expressions and `;`

```csharp
class Employee  
    {  
        public string Name { get; set; }  
        // Expression bodied constructor  
        public Employee(string name) => Name = name;  
        // Expression bodied finalizer  
        ~Employee() => Console.WriteLine("Finalizer Executing");  
    }  

```

## Expression bodied getters and setters

Now expression-bodied members are now allowed also in property accessors

```csharp
public string Name
	{
		get => "get accessor";
		set => Console.WriteLine("set accessor");
	}

```

------

### Further Reading
* [Whats new in C# 7.0](https://blogs.msdn.microsoft.com/dotnet/2016/08/24/whats-new-in-csharp-7-0)
