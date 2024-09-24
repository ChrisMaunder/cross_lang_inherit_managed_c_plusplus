# Cross language inheritance in .NET

An introduction to cross-language inheritence in .NET.



## Introduction

The introduction of .NET brings makes language development simpler than ever. We no longer need to worry about using ATL wrappers in C++ to access VB components, or calling conventions when attempting to interface with FORTRAN code. All .NET enabled languages are now first class entities and interoperate seamlessly, allowing disparate systems to work together more easily than ever, and also allowing developers with different skill sets to work harmoniously together on projects. 

## Creating a .NET component

Creating a component in one language that can be used by another language is very simple. Use the Visual Studio.NET Wizard to create a class library in your favourite .NET language, compile it, and you are done. 

For example, we will create a simple class in C# that exposes two methods: 

```C++
namespace MyCSClass
{using System;
public class CSClass{	public CSClass() { }
	// returns the length of the passed string	public int MyMethod(string str)	{		return str.Length;	}
	// returns n squared	virtual public int MyVirtualMethod(int n)	{		return n*n;	}}
}
```

The first method, `MyMethod`, takes a string object and returns its length. The second method is virtual, and returns the square of the number passed in. Assume that we have compiled this component to *MyCSClass.dll*. 

## Using a .NET component in managed C++

To consume this component using managed C++ we need to first import the assembly into our program: 

```C++
#using "MyCSClass.dll"
```

That's it. No typelibs, no .def files, no ATL headers. We simply use the `#using` statement (and ensure the dll is in the compilers search path) and let the compiler do the rest. 

Once we have imported the class we can then use the `using` declaration to save typing: 

```C++
using namespace MyCSClass;
```

Note the difference here: `#using` is for importing an assembly into your project. `using` specifies the namespace we will be working with, and merely saves typing. 

Actually consuming the class is the same as using any other managed reference type in .NET: 

```C++
CSClass *cs = new CSClass();

int result;
result = cs->MyMethod("Hello World");  // will return 11
result = cs->MyVirtualMethod(2);       // will return 2 squared
```

## Cross language inheritence

Cross language interoperability goes further than simply easing the use of components written in different languages. We can also inherit new classes from components written in other languages, ***without needing the original source code for the component***. 

Imagine you have bought, or otherwise acquired some super-cool component that you love using, but that you wish had one or two more features, or that it did something slightly different. In .NET you can inherit a new class from that component to create a new component that works exactly how you would like. You aren't creating a wrapper for the component: you are creating a new component that derives its properties, methods and fields and that can override the old component's virtual methods and add new methods. 

Back to our example. The method `CSClass::MyVirtualMethod` is virtual, so let's declare a new C++ class that is derived from this C# class, and override that virtual method 

```C++
__gc class CPPClass : public MyCSClass::CSClass
{
public:// returns the cube of the given numbervirtual int MyVirtualMethod(int n){	return n*n*n;}
};
```

We have overridden `CSClass::MyVirtualMethod` with our new method that returns the cube, not the square, of the given number. Once we compile the code we we have a new C++ component that overrides the virtual method in the C# class, and also has the non-virtual `MyMethod()` function. 

```C++
CPPClass *cpp = new CPPClass();

int result;
result = cpp->MyMethod("Hello World"); // will return 11
result = cpp->MyVirtualMethod(2);      // Will output 2 cubed, not 2 squared
```

The accompanying download to this article contains a C# and a VB.NET component that is consumed by, and inherited by, a C++ component. 

## Conclusion

Cross language interoperability allows you to extend 3rd party components with your own functionality in an object oriented fashion. You can easily work with components in any CLS compliant language, and when debugging you are able to step through function calls between components and between languages in the same application. Exception handling is also consistent across languages. If a component in one language throws an exception it can be caught and handled by code written in another language. Most importantly it allows you and your team freedom to choose the language they wish to work in. 

## History

16 Oct 2001 - updated for beta 2
