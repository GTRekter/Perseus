---
layout: post
title: "Data types and memory management in C#"
subtitle: ""
description: "Before explaining the different data types available in C#, it’s important to mention that C# is a strongly-typed language. This means that each variable, constant, input parameter, return type and in general every expression that evaluates to a value, has a type."
excerpt: ""
date: 2020-03-10T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["CSharp", "Dotnet", "Web Development", "Software Development"]
URL: "/2020/03/10/data_types_and_memory_management_in_csharp/images/"
categories: [ Tech ]
---


Before explaining the different data types available in C#, it’s important to mention that C# is a strongly-typed language. This means that each variable, constant, input parameter, return type and in general every expression that evaluates to a value, has a type.

Each type contains information that will be embedded by the compiler into the executable file as metadata which will be used by the common language runtime (CLR) to guarantee **type safety** when it allocates and reclaims memory.

If you wanna know how much memory a specific type allocates, you can use the **sizeof**operator as follows:

```bash
    static void Main()
    {
        var size = sizeof(int);
        Console.WriteLine($"int size:{size}");
        size = sizeof(bool);
        Console.WriteLine($"bool size:{size}");
        size = sizeof(double);
        Console.WriteLine($"double size:{size}");
        size = sizeof(char);
        Console.WriteLine($"char size:{size}");
    }
```

The output will show the number of bytes allocated by each variable.

```bash
    int size:4
    bool size:1
    double size:8
    char size:2
```

The information related to each type are:

- The required storage space.
- The maximum and minimum values. For example, the type Int32 accepts values between -2147483648 and 2147483647.
- The members (methods, fields, events, etc.) contained by the type. For example, if we check the definition of type int, we will find the following struct and members:

```bash
    ...
    namespace System
    {
        [ComVisible(true)]
        public struct Int32 : IComparable, IFormattable, IConvertible, IComparable<Int32>, IEquatable<Int32>
        {        
            public const Int32 MaxValue = 2147483647;       
            public const Int32 MinValue = -2147483648;
            public static Int32 Parse(string s, NumberStyles style, IFormatProvider provider);      
            ...   
        }    
    }
```

- The base type it inherits from.
- The location where the memory for variables will be allocated at run time.
- The kinds of operations that are permitted.

# Memory management

When multiple processes are running on an operating system and the amount of RAM isn’t enough to hold it all, the operating system maps parts of the hard disk with the RAM and starts storing data in the hard disk. The operating system will use than specific tables where virtual addresses are mapped to their correspondent physical addresses to perform the request. This capability to manage the memory is called** virtual memory**.

In each process, the virtual memory available is organized in the following 6 sections but for the relevance of this topic, we will focus only on the **stack**and the **heap**.

![](</data_types_and_memory_management_in_csharp/images/1.png>)


# Stack

The stack is a LIFO (last in, first out) data structure, with a size-dependent on the operating system (by default, for ARM, x86 and x64 machines Windows’s reserve 1MB, while Linux reserve from 2MB to 8MB depending on the version).

This section of memory is **automatically managed by the CPU**. Every time a function declares a new variable, the compiler allocates a new memory block as big as its size on the stack, and when the function is over, the memory block for the variable is deallocated.


# Heap

This region of memory isn’t managed automatically by the CPU and its size is bigger than the stack. When the **_new_**keyword is invoked, the compiler starts looking for the first free memory block that fits the size of the request. and when it finds it, it is marked as reserved by using the built-in C function **malloc()** and a return the pointer to that location. It’s also possible to deallocate a block of memory by using the built-in C function **free()**. This mechanism causes memory fragmentation and has to use pointers to access the right block of memory,** it’s slower than the stack **to perform the read/write operations.


# Custom and Built-in types

While C# provides a standard set of built-in types representing integers, boolean, text characters, and so on, You can use constructs like struct, class, interface, and enum to create your own types.

An example of custom type using the struct construct is:

```bash
    struct Point
    {
        public int X;
        public int Y;};
```

# Value and reference types

We can categorize the C# type into the following categories:

- Value types
- Reference types

The following scheme shows the hierarchy of both reference types (String, Array, etc.) and value types. As you can see both data types derive from the class **System.Object**.

![](</images/data_types_and_memory_management_in_csharp/2.png>)


# Value types

Value types derive from the **System.ValueType**class and variables of this type contain their values within their memory allocation in the stack. The two categories of value types are **struct**and **enum**.

The following example shows the member of the type boolean. As you can see there is no explicit reference to System.ValueType class, this happens because this class is inherited by the struct.

```bash  
    ...
    namespace System
    {
        [ComVisible(true)]
        public struct Boolean : IComparable, IConvertible, IComparable<Boolean>, IEquatable<Boolean>
        {
            public static readonly string TrueString;
            public static readonly string FalseString;
            public static Boolean Parse(string value);
            ...
        }
    }
```

# Reference types

On the other hand, the reference types do not contain the actual data stored in a variable, but the **memory address of the heap**where the value is stored. The categories of reference types are **classes**, **delegates**, **arrays**, and **interfaces**.

![](</images/data_types_and_memory_management_in_csharp/3.png>)

At run time, when a reference type variable is declared,** it contains the value null** until an object that has been created using the keywords **_new_**is assigned to it.

The following example shows the members of the generic type List&lt;T>.

```bash
    ...
    namespace System.Collections.Generic
    {
        [DebuggerDisplay("Count = {Count}")]
        [DebuggerTypeProxy(typeof(Generic.Mscorlib_CollectionDebugView<>))]
        [DefaultMember("Item")]
        public class List<T> : IList<T>, ICollection<T>, IEnumerable<T>, IEnumerable, IList, ICollection, IReadOnlyList<T>, IReadOnlyCollection<T>
        {
            ...
            public T this[int index] { get; set; }
            public int Count { get; }
            public int Capacity { get; set; }
            public void Add(T item);
            public void AddRange(IEnumerable<T> collection);
            ...
        }
    }
```

In case you wanna find out the memory address of a specific object, the class **System.Runtime.InteropServices** provides a way to access to managed objects from unmanaged memory. In the following example, we are gonna use the static method **GCHandle.Alloc()** to allocate a handle to a string and then the method **AddrOfPinnedObject** to retrieve its address.

```bash
    string s1 = "Hello World";
    GCHandle gch = GCHandle.Alloc(s1, GCHandleType.Pinned);
    IntPtr pObj = gch.AddrOfPinnedObject();
    Console.WriteLine($"Memory address:{pObj.ToString()}");
```

The output will be

```bash
    Memory address:39723832
```

# Video

<https://www.youtube.com/watch?v=_7AL8wDl5Tc>
