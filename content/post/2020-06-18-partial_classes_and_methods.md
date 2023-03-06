---
layout: post
title: "Partial classes and methods"
subtitle: ""
description: "In this article, I’m going to explain what partial classes and partial methods are, and how to implement them in C#."
excerpt: ""
date: 2020-06-18T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["CSharp", "Dotnet", "Web Development", "Software Development"]
URL: "/2020/06/18/partial_classes_and_methods/"
categories: [ Tech ]
---

In this article, I’m going to explain what partial classes and partial methods are, and how to implement them in C#.

In C#, the identifier used to identify a class must be unique; however, when working on large scale projects, it might be necessary that multiple developers work on the same class or methods simultaneously. For this reason, Microsoft introduced the **keyword partial** and added this new feature in C# 2.0.

The keyword partial allows **splitting a class definition** into different parts **within the same namespace,**which will be combined when the application is compiled.

To use this feature, each part has to:

- Use the **partial**keyword.
- Be available at compile time.
- Have the same access modifier (**public**, **private**, **protected**and **internal**)
- Be in the same namespace as the other parts.

It’s also important to mention that:

- If even one part is declared **abstract**, then the whole type is considered abstract.
- If even one part is declared **sealed**, then the entire type is considered sealed.
- If even one part declares a base type, then the whole type inherits that class (even parts that omit a base class still inherit the base type).
- Any class, struct, or interface members declared in a part are available to all the other parts.

For example, if we consider the following parts:

**PartialClassFirstFile.cs**
```bash
    partial class Earth : Planet, IRotate
    {
       public void CalcolateMass() { … }
    }
```

**PartialClassSecondFile.cs**
```bash
    partial class Earth : IRevolve
    {
       public void CalcolateSpeed() { … }
    }
```

Once the compilation is over, the equivalent class is:

```bash
    class Earth : Planet, IRotate, IRevolve
    {
       public void CalcolateMass() { … }
       public void CalcolateSpeed() { … }
    }
```

The partial keyword can also be **applied to methods**. Its behavior is very similar to the classes, in fact, a class can contain the signature of the method and another part can supply its implementation. Both parts will be then united at compile time.

If no implementation is provided, the compiler will optimize the code by removing both method and all calls at compile time.

It’s import to mention that partial method:

- must return **void**.
- can have **in**or **ref**but not **out**parameters (**out**params must be assigned and since a partial method might be not implemented, the use of this kind of parameters is not allowed).
- are implicitly **private**(consequently cannot be virtual).
- can have **static**and unsafe **modifiers**.
- can be generics.

In the following example, we will add a little more code to the previous code.

**PartialClassFirstFile.cs**
```bash
    partial class Earth : Planet, IRotate
    {
       public void CalcolateMass() { … }
       public partial void CalcolateVolume() { }
       public partial void CalcolateForce() { }
    }
```

**PartialClassSecondFile.cs**
```bash
    partial class Earth : IRevolve
    {
       public void CalcolateSpeed() { … }
       public partial void CalcolateVolume() { … }
    }
```

In this case, the class Earth, implemented in the file PartialClassFisrtFile.cs declares only the method signature of both CalcolateVolume and CalcolateForce. While the method CalcolateVolume is implemented in the relative class in the file PartialClassSecondFile.cs, the other method is not which means that once the compilation is over, the _CalcolateForce_method will be removed and the final class will be:

```bash
    class Earth : Planet, IRotate, IRevolve 
    {
       public void CalcolateMass() { … }
       public void CalcolateVolume() { … }
       public void CalcolateSpeed() { … }
    }
```