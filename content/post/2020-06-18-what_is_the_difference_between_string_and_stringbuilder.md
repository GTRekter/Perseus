---
layout: post
title: "What is the difference between String and StringBuilder?"
subtitle: ""
description: "Difference between the String and StringBuilder is an important concept that makes the difference when an application has to deal with the editing of a high number of Strings..."
excerpt: ""
date: 2020-06-18T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["CSharp", "Dotnet", "Web Development", "Software Development"]
URL: "/2020/06/18/what_is_the_difference_between_string_and_stringbuilder/"
categories: [ Tech ]
---


Difference between the **String**and **StringBuilder**is an important concept that makes the difference when an application has to deal with the editing of a high number of Strings.


# String

The String object is a collection of **UTF-16** code units represented by a **System.Char** object which belongs to the System namespace. Since the value of this object is **read-only**, the entire object String has defined as **immutable**. The maximum size of a String object in memory is **2 GB**, or about **1 billion characters**.


# Immutable

Being immutable means that every time a method of the System.String is used, a new string object is created in memory, and this causes a new allocation of space for the new object.

For example, by using the string concatenation operator += appears that the value of the string variable named test change. It generate a new String object, which has a different value and address from the original and assign it to the test variable.

```bash
    string test;
    test += “red”; // a new object string is created
    test += “coding”; // a new object string is created
    test += “planet”; // a new object string is created
```

# StringBuilder

The StringBuilder is a dynamic object which belongs to the **System.Text** namespace and allow to modify the number of characters in the string that it encapsulates; this characteristic is called **mutability**.


# Mutability

A StringBuilder maintains a buffer to accommodate expansions to the string to be able to append, remove, replace, or insert characters. If there is space available in the buffer, new data are added. Otherwise, a new, larger buffer is allocated, data from the original buffer is copied to the new buffer, and the new data is then appended to the new buffer.

```bash
    StringBuilder sb = new StringBuilder(“”);
    sb.Append(“red”);
    sb.Append(“blue”);
    sb.Append(“green “);
    string colors = sb.ToString();
```

# Length, Capacity, and MaxCapacity

- **StringBuilder.Length:**this** **property defines the number of characters currently contained by the StringBuilder instance.
- **StringBuilder.Capacity:**this property specifies the number of characters that the current instance can contain. Every time the number of added characters make the length become higher than the capacity, the values of the capacity property is doubled.
- **StringBuilder.MaxCapacity:** this property defines the maximum size, which can be allocated to the StringBuilder. When the maximum capacity is reached, no further memory can be allocated for the StringBuilder object. Trying to add characters or expand it beyond its maximum capacity throws either an **ArgumentOutOfRangeException**or an **OutOfMemoryException**exception.

```bash
    StringBuilder sb = new StringBuilder();
    Console.WriteLine(“Capacity: {0} | Length: {1}”, sb.Capacity, sb.Length);
    sb.Append(“This is a sentence.”);
    Console.WriteLine(“Capacity: {0} | Length: {1}”, sb.Capacity, sb.Length);
    for (int ctr = 0; ctr < 3; ctr++) 
    {
       sb.Append(“This is an additional sentence.”);
       Console.WriteLine(“Capacity: {0} | Length: {1}”, sb.Capacity, sb.Length);
    }
    // Output
    // Capacity: 16 | Length: 0
    // Capacity: 32 | Length: 19
    // Capacity: 64 | Length: 50
    // Capacity: 128 | Length: 81
    // Capacity: 128 | Length: 112
```

# Performances

In order to help you better understand the performance difference between String and StringBuilder, I created the following example:

```bash
    Stopwatch timer = new Stopwatch();
    string str = string.Empty;
    timer.Start();
    for (int i = 0; i < 10000; i++) 
    {
       str += i.ToString();
    }
    timer.Stop();
    Console.WriteLine(“String : {0}”, timer.Elapsed);
    timer.Restart();
    StringBuilder sbr = new StringBuilder(string.Empty);
    for (int i = 0; i < 10000; i++) 
    {
       sbr.Append(i.ToString());
    }
    timer.Stop();
    Console.WriteLine(“StringBuilder : {0}”, timer.Elapsed);
    // Output
    // String : 00:00:00.0706661
    // StringBuilder : 00:00:00.0012373
```