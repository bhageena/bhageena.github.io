---
layout: post
title: What's new in C Sharp 6
description: "The rich feature list of C# 6, allows us to write more concise and readable code. The syntax avoids verbosity in many common scenarios and it leads to better developer productivity by helping us concentrate on developing the core features rather than getting entangled in the constructs of the language"
excerpt: ""
categories: blog
tags: CSharp .Net NewReleases Improvements
comments: true
share: true
readtime: true
---
Since its first release in January 2002 (Version 1.0), C# has come a long way. Along with .NET Framework 4.6, a new version of C#, has been released in July 2015 - though the language itself hasn't changed much, main thrust has been on introduction of Roslyn .NET Compiler Platform. However, version 6.0 consists of a rich list of syntactical improvements, leading to code that is more concise & more readable resulting in enhanced developer productivity. We'll look into the feature list, try out small examples and will see how this release changes the way we write C#.

# What's New in C# 6

C# has always been my favorite language, and it's getting more feature rich and attractive with every new release. Let's have a peek at the features in this release:

- [Auto-Property Initializers](#auto-property-initializers)

- [await in catch and finally blocks](#await-in-catch-and-finally-blocks)

- [Exception Filters](#exception-filters)

- [Expression-bodied Function Members](#expression-bodied-function-members)

- [Improvements in Overload Resolution Methods](#improvements-in-overload-resolution-methods)

- [index initializers](#index-initializers)

- [nameof Expressions](#nameof-expressions)

- [Null - Conditional Operators](#null-conditional-operators)

- [String Interpolation](#string-interpolation)

- [using static](#using-static)

Introduction of these features allows us to write more concise and readable code. The syntax avoids verbosity in many common scenarios and it leads to better developer productivity by helping us concentrate on developing the core features rather than getting entangled in the constructs of the language. Also while reading the code the improved syntax helps in understanding the design intent more easily.

In the sections to follow, we'll look into each of these features using some examples.

### Auto-Property Initializers
Earlier in order to make a read-only property we had to the following:
- A read-only-defined backing field
- Initialization of the backing field from within the constructor
- Explicit implementation of the property (rather than using an auto-property)
- An explicit getter implementation that returns the backing field  
These properties would need to have setters and you would need to use that setter to initialize the data storage used by the backing field.
E.g., we used to create our properties with a getter and setter and initialized them in constructor with values as shown below.

- #### without initializers

```csharp
        public class Employee
        {
            public string Name { get; set; }
            public decimal Salary { get; set; }
            public Employee()
            {
            	 /* Initializing property through constructor */
            	  Name = "Douglas Bader";
                Salary = 50000;
            }
        }
```
As this class grows, you may include other constructors. Each constructor needs to initialize these fields, or you'll introduce errors.

Now in C#6, *Auto-Property Initializers* allows us to declare initial value for a property as part of the property declaration.

- #### with initializers

```csharp
        public class Employee
        {
           /* Property with inline initialization */
           public string Name { get; set; } = "Douglas Bader";
           public decimal Salary { get; set; } = 50000;
        }
```


### Read-only auto-properties

In addition to flexibility with visibility, you can also initialize *Read-only auto-properties*. They provide a more concise syntax to create immutable types. The closest you could get to immutable types in earlier versions of C# was to declare private setters:

```csharp
public string  Name   { get; private set; };
public decimal Salary { get; private set; };
```

Using this syntax, the compiler doesn't ensure that the type really is immutable. It only enforces that the `Name` and `Salary` properties are not modified from any code outside the class.

Read-only auto-properties enable true read-only behavior. You declare the auto-property with only a get accessor:

```csharp
public string  Name   { get; };
public decimal Salary { get; };
```
The `Name` and `Salary` properties can be set only in the body of a constructor:

```csharp
public Employee(string name, double salary)
{
    if (IsNullOrWhiteSpace(name))
        throw new ArgumentException(message: "Cannot be blank", paramName: nameof(name));
    Name = name;
    Salary = salary;
}
```

Trying to set `Name` in another method generates a `CS0200` compilation error:

```csharp
public class Employee
{
    public string  Name   { get; };

    public void ChangeName(string newName)
    {
        // Generates CS 0200: Property or indexer cannot be assigned to -- it is read only
        Name = newName;
    }
}
```
This feature enables true language support for creating immutable types and using the more concise and convenient auto-property syntax.

## Await in Catch and Finally blocks

C# 5 not allowed to use the `await` keyword in `catch` and `finally` blocks, Now in any async method, you can use an await expression in a finally clause, you can also await in catch expressions. This is most often helpful while logging errors:

```csharp
        public async static Task GetStockQuotes()
        {
            await logMethodEntrance();
            HttpClient client = new HttpClient();
            try
            {
                var result = await client.GetStringAsync("http://finance.yahoo.com/webservice/v1/symbols/allcurrencies/quote?format=json");
                WriteLine(result);
            }
            catch (Exception exception)
            {
            	 /* If the first request throws an exception, this request will be executed. Both are asynchronous requests*/
                var result = await client.GetStringAsync("http://finance.yahoo.com/webservice/v1/symbols/allcurrencies/quote");
                WriteLine(result);
            }
            finally
            {
                 await logMethodExit();
                 client.Dispose();
            }   
        }
```
The implementation details for adding `await` support inside `catch` and `finally` clauses ensures that the behavior is consistent with the behavior for synchronous code. When code executed in a `catch` or `finally` clause throws, execution looks for a suitable `catch` clause in the next surrounding block. If there was a current exception, that exception is lost. The same happens with awaited expressions in `catch` and `finally` clauses: a suitable `catch` is searched for, and the current exception, if any, is lost.  

## Exception Filters

Exception Filters are clauses that determine when a given catch clause should be applied. If the expression used for an exception filter evaluates to  `true`, the catch clause performs its normal processing on an exception. If the expression evaluates to `false`, then the `catch` clause is skipped.

Exception filters have been supported in Visual Basic, but are new to the C# compiler. They allow you to specify a condition for a catch block. We typically used the following in C# 5:

```csharp
class Program
{
    static void Main(string[] args)
    {
        var httpStatusCode = 404;
        Write("HTTP Error: ");

        try
        {
            throw new Exception(httpStatusCode.ToString());
        }
        catch (Exception ex)
        {
            if (ex.Message.Equals("500"))
                Write("Bad Request");
            else if (ex.Message.Equals("401"))
                Write("Unauthorized");
            else if (ex.Message.Equals("402"))
                Write("Payment Required");
            else if (ex.Message.Equals("403"))
                Write("Forbidden");
            else if (ex.Message.Equals("404"))
                Write("Not Found");
        }

        ReadLine();
    }
}
```
Rather than entering the catch block and checking to see which condition met our exception, we can now decide if we even want to enter the specific catch block.

```csharp
    class Program
    {
        static void Main(string[] args)
        {
            var httpStatusCode = 404;
            Write("HTTP Error: ");

            try
            {
                throw new Exception(httpStatusCode.ToString());
            }
            catch (Exception ex) when (ex.Message.Equals("400"))
            {
                Write("Bad Request");
                ReadLine();
            }
            catch (Exception ex) when (ex.Message.Equals("401"))
            {
                Write("Unauthorized");
                ReadLine();
            }
            catch (Exception ex) when (ex.Message.Equals("402"))
            {
                Write("Payment Required");
                ReadLine();
            }
            catch (Exception ex) when (ex.Message.Equals("403"))
            {
                Write("Forbidden");
                ReadLine();
            }
            catch (Exception ex) when (ex.Message.Equals("404"))
            {
                Write("Not Found");
                ReadLine();
            }

            ReadLine();
        }
    }
```
The code generated by exception filters provides better information about an exception that is thrown and not processed.

## Expression-bodied function members

You can now write functions and computed properties like lamda expressions to save unnecessary ceremony of defining function and property statement block. Just use the lambda operator `=>` and then start writing your code that goes into your function/property body. Here is a simple example:

```csharp
  public static double MultiplyNumbers(double num1, double num2) => num1 * num2;
```
You can use expression-bodied members in read only properties as well:

```csharp
public string FullName => $"{FirstName} {LastName}";
```

## Improvements in Overload Resolution Methods

This feature is probably easy to miss. There were constructs where the previous version of the C# compiler may have found some method calls involving lambda expressions ambiguous.

```csharp
   class Program
    {
        private static void DoProcessing(Action item)
        {

        }
        private static int DoProcessing(Func<int> item)
        {
            return item();
        }
        public static int FunCall()
        {
            return 50;
        }
        static void Main(string[] args)
        {
            int resultitem = DoProcessing(FunCall);
            Console.WriteLine(resultitem);
            Console.ReadKey();
        }
    }
```
In earlier versions, this code fails to compile. The compiler issues an Error- “The call is ambiguous between the following methods or properties: `DoProcessing(System.Action)` and `DoProcessing(System.Func<int>)`". There is also an error that `FunCall` has the wrong return type, and that the void cannot be implicitly converted to int. In C# 6, however, this compiles without issue.

## Index Initializers

Index initializers make, creation and initialization of objects with indexes, possible to at the same time.
This feature makes working with Dictionary initialization very easy:

```csharp
var dict = new Dictionary<string, int>()
{
    ["firstName"] = "Douglas",
    ["lastName"] = "Bader"
};
```
Any object that has an indexed getter or setter can be used with this syntax:

```csharp
class Program
{
    public class ClassWithIndexer
    {
        public int this[string index]
        {
            set
            {
                Console.WriteLine($"Index: {index}, value: {value}");
            }
        }
    }

    public static void Main()
    {
        var x = new ClassWithIndexer()
        {
          ["firstName"] = "Douglas",
          ["lastName"] = "Bader"
        };

        Console.ReadKey();
    }
}
```
If the class has multiple indexers it is possible to assign them all in a single group of statements:

```csharp
class Program
{
    public class ClassWithIndexer
    {
        public int this[string index]
        {
            set
            {
                Console.WriteLine($"Index: {index}, value: {value}");
            }
        }
        public string this[int index]
        {
            set
            {
                Console.WriteLine($"Index: {index}, value: {value}");
            }
        }
    }

    public static void Main()
    {
        var x = new ClassWithIndexer()
        {
            ["firstName"] = "Douglas",
            ["lastName"] = "Bader"
            ["role"] = "Fighter Pilot",
            ["level"] = "Ace"
        };
    }
}

```
One important thing you should  notice here is that the set accessor might behave differently compared to an [Add method used in collection initializers](#extension-add-methods-in-collection-initializers). As shown in the below examples:

Here setting value in same property twice doesn't throw any exception, but first value gets overwritten by second one.

```csharp
var names = new Dictionary<string, string>
{
    ["firstName"] = "Douglas",
    ["firstName"] = "Bader", // does not throw, second value overwrites the first one
  };
```

Doing the same thing, as in below example throws run time exception.

```csharp
var names = new Dictionary<string, string>
{
    { "firstName", "Douglas" },
    { "firstName", "Bader" }, // run-time ArgumentException: An item with the same key has already been added.
};
```

The new syntax ensures that the associative containers can be initialized same way in which we have been initializing sequential containers from many versions.

### Extension `Add` methods in collection initializers

When you use a collection initializer to create a collection, the compiler searches for an Add method of the collection type for which the parameters for the Add method match the types of the values in the collection initializer. This Add method is used to populate the collection with the values from the collection initializer.

If no matching Add method exists and you cannot modify the code for the collection, you can add an extension method called Add that takes the parameters that are required by the collection initializer. This is typically what you need to do when you use collection initializers for generic collections.

The following example shows how to add an extension method to the generic List<T> type so that a collection initializer can be used to add objects of type Employee. The extension method enables you to use the shortened collection initializer syntax.

```csharp
public class Fighter
{
	public int Id { get; set; }
	public string Name { get; set; }
}
```

```csharp
public static void Add(this List<Fighter> list, int id, string name)
	{
		list.Add(new Fighter {
			Id = id,
			Name = name
		});
	}

```

```csharp
public void Main()
{
	dynamic fighters = new List<Fighter> {
		{
			1,
			"Erich, Hartmann"
		},
		{
			2,
			"Manfred, Richthofen"
		},
		{
			3,
			"Douglas, Bader"
		}
	};
}

```

## `nameof` Expressions

The nameof operator allows you to get the name of a code element such as variable, type or member as string, without hard-coding it as a literal. The operation is evaluated at compile-time, which means you can rename, using an IDE's rename feature, a referenced identifier and the name string will update with it. This is also useful when throwing exceptions related to method arguments and also when implementing INotifyPropertyChanged.

The `nameof` expression evaluates to the name of a symbol. It's very handy whenever you need the name of a variable, a property, or a member field.
One of the most common uses for `nameof` is to provide the name of a symbol that caused an exception:

```csharp
public string SayHello(string greeted)
{
    if (greeted == null)
        throw new ArgumentNullException(nameof(greeted));

    Console.WriteLine("Hello, " + greeted);
}
```

The nameof operator is evaluated at compile time and changes the expression into a string literal. This is also useful for strings that are named after their member that exposes them. e.g.:

```csharp
public static class Strings
{
    public const string Foo = nameof(Foo); // Rather than Foo = "Foo"
    public const string Bar = nameof(Bar); // Rather than Bar = "Bar"
}
```

Since nameof expressions are compile-time constants, they can be used in attributes, case labels, switch statements, and so on.

nameof can be used with Enums very conveniently.
```csharp
// So instead of writing
Console.WriteLine(Enum.One.ToString());

//you can write
Console.WriteLine(nameof(Enum.One))
```
The nameof operator can access non-static members using static-like syntax.
```csharp
// So instead of doing
string foo = "Foo";
string lengthName = nameof(foo.Length);

//you can write
string lengthName = nameof(string.Length);
```

The output will be Length in both examples. However, the latter prevents the creation of unnecessary instances.

Although the nameof operator works with most language constructs, there are some limitations. For example, you cannot use the nameof operator on open generic types or method return values:

```csharp
public static int Main()
{   
    Console.WriteLine(nameof(List<>)); // Compile-time error
    Console.WriteLine(nameof(Main())); // Compile-time error
}
```
Furthermore, if you apply it to a generic type, the generic type parameter will be ignored:
```csharp
Console.WriteLine(nameof(List<int>));  // "List"
Console.WriteLine(nameof(List<bool>)); // "List"
```

The advantage of using the `nameof` operator over a constant string is that tools can understand the symbol. If you use refactoring tools to
rename the symbol, it will rename it in the `nameof` expression. Constant strings don't have that advantage. Try it yourself in your favorite editor:
rename a variable, and any `nameof` expressions will update as well.

The `nameof` expression produces the unqualified name of its argument
(`LastName` in the previous examples) even if you use the fully qualified
name for the argument:

```csharp
public string FirstName
{
    get { return firstName; }
    set
    {
        if (value != firstName)
        {
            firstName = value;
            PropertyChanged?.Invoke(this,
                new PropertyChangedEventArgs(nameof(UXComponents.ViewModel.FirstName)));
        }
    }
}
private string firstName;
```

This `nameof` expression produces `FirstName`, not `UXComponents.ViewModel.FirstName`.


## Null-conditional operators
The NullReferenceException is the most common type of exception in C#, avoiding it requires couple of lines of null checks that look ugly,
moreover they complicate code. You need to check every access of variables to ensure you are not dereferencing `null`, otherwise you will be greeted with a runtime exception. The *null conditional operator* makes those checks much easier and fluid.

You only need to replace the member access `.` with `?.`:

So instead of writing with explicit null check
```csharp
var name = employee != null ? employee.Name : null;
```
using null conditional operator, you can write as thus:
```csharp
var name = employee?.Name;
```

In the above example, the variable `name` is assigned `null` if the employee object is `null`. Otherwise, it gets assigned the value of the `Name` property. Most importantly, the `?.` means that this line of code does not generate a `NullReferenceException` when the `employee` variable is `null`. Instead, it short-circuits and produces `null`.

Also, note that this expression returns a `string`, regardless of the value of `employee`. In the case of short circuiting, the `null` value returned is typed to match the full expression.

You can often use this construct with the *null coalescing* operator to assign default values when one of the properties are `null`:

```csharp
name = employee?.Name ?? "Unspecified";
```

The right hand side operand of the `?.` operator is not limited to properties or fields. You can also use it to conditionally invoke methods. The most common use of member functions  with the null conditional operator is to safely invoke delegates (or event handlers) that may be `null`.  You'll do this by calling the delegate's `Invoke` method using the `?.` operator to access the member.

## String Interpolation
String Interpolation is the process of evaluating a string literal containing one or more placeholders, yielding a result in which the placeholders are replaced with their corresponding values. C# 6 contains new syntax for composing strings from a format string and expressions that can be evaluated to produce other string values.

Traditionally, you needed to use positional parameters in a method
like `string.Format`:

```csharp
public string Name
{
    get
    {
        return string.Format("{0} {1}", FirstName, LastName);
    }
}
```
With C# 6, the new string interpolation feature enables you to embed the expressions in the format string. Simple preface the string with `$`:

```
public string FullName => $"{FirstName} {LastName}";
```

This initial example used variable expressions for the substituted expressions. You can expand on this syntax to use any expression. For
example, you could compute a employee's grade point average as part of the interpolation:

```
public string GetFormattedGradePoint() =>
    $"Name: {LastName}, {FirstName}. G.P.A: {paygrades.Average()}";
```

Running the preceding example, you would find that the output for `paygrades.Average()` might have more decimal places than you would like. The string interpolation syntax supports all the format strings available using earlier formatting methods. You add the format strings inside the braces. Add a `:` following the expression to format:

```
public string GetGradePointPercentage() =>
    $"Name: {LastName}, {FirstName}. G.P.A: {paygrades.Average():F2}";
```

The preceding line of code will format the value for `paygrades.Average()` as a floating-point number with two decimal places.

The `:` is always interpreted as the separator between the expression being formatted and the format string. This can introduce problems when
your expression uses a `:` in another way, such as a conditional operator:

```
public string GetGradePointPercentages() =>
    $"Name: {LastName}, {FirstName}. G.P.A: {paygrades.Any() ? paygrades.Average() : double.NaN:F2}";
```

In the preceding example, the `:` is parsed as the beginning of the format string, not part
of the conditional operator. In all cases where this happens, you can
surround the expression with parentheses to force the compiler to interpret
the expression as you intend:

```
public string GetGradePointPercentages() =>
    $"Name: {LastName}, {FirstName}. G.P.A: {(paygrades.Any() ? paygrades.Average() : double.NaN):F2}";
```

There aren't any limitations on the expressions you can place between the braces. You can execute a complex LINQ query inside an interpolated
string to perform computations and display the result:

```
public string GetAllpaygrades() =>
    $@"All paygrades: {paygrades.OrderByDescending(g => g)
    .Select(s => s.ToString("F2")).Aggregate((partial, element) => $"{partial}, {element}")}";
```

You can see from this sample that you can even nest a string interpolation expression inside another string interpolation expression. This example
is very likely more complex than you would want in production code. Rather, it is illustrative of the breadth of the feature. Any C# expression
can be placed between the curly braces of an interpolated string.

## using static

In C# 6, we can import the static members of a class into our namespace.  This means we can use the static members of a class directly with no need to qualify them with their namespace or type name. The format is `using static` followed by the type whose static methods you wish to import.  

Often we make abundant use of static methods in our code e.g. the utility methods. Calling the static methods requires it's class name to precede it. Repeatedly typing the class name can obscure the meaning of your code. A common example is when you write classes that perform many numeric calculations.
Your code will be littered with @System.Math.Sin, @System.Math.Sqrt and other calls to different methods in the @System.Math class. The new `using static` syntax can make these classes much cleaner to read. You specify the class you're using:

```csharp
using static System.Math;
```

And now, you can use any static method in the @System.Math class without qualifying the @System.Math class. The @System.Math class is a great use case for
this feature because it does not contain any instance methods. You can also use `using static` to import a class' static methods for a class that has both static and instance methods. One of the most useful examples is @System.String:

```csharp
using static System.String;
```

> You must use the fully qualified class name, `System.String` in a static using statement.  You cannot use the `string` keyword instead.

You can now call static methods defined in the @System.String class qualifying those methods as members of that class:

```csharp
if (IsNullOrWhiteSpace(lastName))
    throw new ArgumentException(message: "Cannot be blank", paramName: nameof(lastName));
```

The `static using` feature and extension methods interact in interesting ways, and the language design included some rules that specifically address those interactions. The goal is to minimize any chances of breaking changes in existing codebases, including yours.

Extension methods are only in scope when called using the extension method invocation syntax, not when called as a static method. You'll often see this in LINQ queries. You can import the LINQ pattern by importing @System.Linq.Enumerable.

```csharp
using static System.Linq.Enumerable;
```

This imports all the methods in the @System.Linq.Enumerable class. However, the extension methods are only in scope when called as extension methods. They are not in scope if they are called using the static method syntax:

```csharp
public bool MakesDeansList()
{
    return paygrades.All(g => g > 3.5) && paygrades.Any();
    // Code below generates CS0103:
    // The name 'All' does not exist in the current context.
    //return All(paygrades, g => g > 3.5) && paygrades.Any();
}
```
This decision is because extension methods are typically called using extension method invocation expressions. In the rare case where they are called using the static method call syntax it is to resolve ambiguity. Requiring the class name as part of the invocation seems wise.

Apart from these, the `static using` directive also imports any nested types. That enables you to reference any nested types without qualification.

------
