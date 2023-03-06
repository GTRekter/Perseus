---
layout: post
title: "What is a virtual member?"
subtitle: ""
description: "We can’t talk about virtual members without referring to polymorphism. In fact, a function, property, indexer or event in a base class marked as virtual will allow override from a derived class..."
excerpt: ""
date: T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["CSharp", "Dotnet", "Web Development", "Software Development"]
URL: "/2020/03/12/what_is_a_virtual_member/"
categories: [ Tech ]
---

We can’t talk about virtual members without referring to polymorphism. In fact, a function, property, indexer or event in a base class marked as virtual will allow override from a derived class. By default, members of a class are non-virtual and cannot be marked as that if static, abstract, private, or override modifiers.

For example, let’s consider the ToString() method in System.Object. Because this method is a member of System.Object it’s inherited in all classes and will provide the ToString() methods to all of them.

```bash
    namespace VirtualMembersArticle
    {
       public class Company
       {
           public string Name { get; set; }
       }   
       class Program
       {
           static void Main(string[] args)
           {
               Company company = new Company() { Name = "Microsoft" };
               Console.WriteLine($"{company.ToString()}");
               Console.ReadLine();
           }  
       }
    }
```

The output of the previous code is:

```bash
    VirtualMembersArticle.Company
```

Let’s consider that we want to change the standard behavior of the _ToString()_ methods inherited from** System.Objec**t in our Company class. To achieve this goal it’s enough to use the **override keyword** to declare another implementation of that method.

```bash
    public class Company
    {
       ...
       public override string ToString()
       {
           return $"Name: {this.Name}";
       }
    }
```

Now, when a virtual method is invoked, the run-time will check for an overriding member in its derived class and will call it if present. The output of our application will then be:

```bash
    Name: Microsoft
```

In fact, if you check the **System.Object** class you will find that the method is marked as virtual.

```bash
    namespace System
    {
       [NullableContextAttribute(2)]
       public class Object
       {
           ....
           public virtual string? ToString();
           ....
       }
    }
```
