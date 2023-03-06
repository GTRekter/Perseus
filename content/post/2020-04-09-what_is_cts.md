---
layout: post
title: "What is CTS?"
subtitle: ""
description: ".NET is language-agnostic, which allows programmers to write code in different languages (which can be compiled to IL), and that code can interact with other code written in different languages..."
excerpt: ""
date: 2020-04-09T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["CSharp", "Dotnet", "Web Development", "Software Development"]
URL: "/2020/04/09/what_is_cts/"
categories: [ Tech ]
---

.NET is **language-agnostic**, which allows programmers to write code in different languages (which can be compiled to IL), and that code can interact with other code written in different languages.

This feature is provided by the CTS (Common Type System), a standard that specifies how type definitions are represented in the memory and how types are declared, used, and managed in the CLR (Common Language Runtime).

For example, C# has an **int**data type, and VB.NET has an **Integer**data type. After the compilation, both instances of int and Integer will use the same structure **Int32**from CTS.

The functions performed by CTS are:

- Establishes a framework that enables cross-language integration.
- It provides an object-oriented model that supports the implementation of different programming languages.
- Defines a set of rules that languages must follow when working with types.
- Provides a library that contains the basic primitive types that are used in application development (i.e., Boolean, Byte, Char, etc.)

The CTS defines the following types:

1. **Reference type:**Reference types are allocated on the heap and stores a reference to the value’s memory address.
2. **Value type:**Value types are allocated on the stack and directly contain their data.

It also defines the following categories, each with their specific semantics and usage:

- **Classes:**Reference type that can be derived from another class, and that is derived implicitly from **System.Object.**
- **Structures:** Value type that derives implicitly from **System.ValueType,**which is derived from **System.Object.**
- **Enums:**Value type that inherits directly from **System.Enum.**
- **Interfaces:**Contract that specifies a “can do” relationship or a “has a” relationship.
- **Delegates:**Reference types that serve a purpose similar to that of function pointers in C++.
