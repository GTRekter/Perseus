---
layout: post
title: "Demystifying ASP.NET Core"
subtitle: ""
description: "ASP.NET Core is becoming increasingly indispensable in the web development landscape. This article aims to simplify the concepts behind ASP.NET Core and how it works..."
excerpt: ""
date: 2020-08-11T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["CSharp", "Dotnet", "Web Development", "Software Development"]
URL: "/2020/08/11/demystifying_aspnet_core/"
categories: [ Tech ]
---

ASP.NET Core is becoming increasingly indispensable in the web development landscape. This article aims to simplify the concepts behind ASP.NET Core and how it works.


# What is .NET?

.NET is a **platform** that supports many different languages, such as VB.NET, C#, and C++. It provides the tools and libraries necessary to interact with the operative system and build any type of app, including web, mobile, desktop, and games.


# The code’s journey in .NET

Because the languages supported by .NET are high-level languages, they need to be ‘translated’ into a low-level language that closely reflects the **CPU** operations. This operation is performed by specific programs called **compilers**.

As mentioned before, .NET supports different programming languages, and each of them has it’s own compiler (C# language has got cse.exe compiler and VB.NET has got vbc.exe compiler).

However, these compilers don’t convert your code into native code, but instead into a stack-based assembly language called **Intermediate language (IL)**. IL enables the platform- and CPU-independence feature of the .NET framework, by allowing compiled source code to be executed in any environment supporting the CLI specification.

At execution time, the IL code is taken by the .NET runtime (CLR) where a specific compiler called **Just-in-time (JIT) compiler** examinee the type safety of the code and converts the MSIL as needed during execution and stores the resulting native code in memory so that it is accessible for subsequent calls in the context of that process. This process provides better performance, preserves memory, and saves time during application initialization.

![](</images/demystifying_aspnet_core/1.gif>)


# What is ASP.NET?

ASP.NET is an open-source **web framework** that extends the .NET platform by providing tools and libraries (like web-page templating syntax and libraries to implement common web patterns like MVC) to build web applications.


## Why another ASP.NET

The development of ASP.NET Core was motivated by the desire to create a web framework with four main goals:

- To be **cross-platform**
- To have a **modular architecture**
- To be completely **open-source**


## Open source and fast releases cycles

Unlike .NET framework, which had long releases cycles, .NET Core, thanks to its open-source nature where any developer can publicly propose a fix, has a faster release cadence.

![](</images/demystifying_aspnet_core/2.png>)

<https://github.com/dotnet/core>


## Modularity

ASP.NET Core has a modular design. This means that, unlike ASP.NET, where you had to install the entire .NET framework, you start with a bare-bones application and only include the dependencies you need. This modularity results in an improvement in performance.

![](</images/demystifying_aspnet_core/3.png>)

<https://www.techempower.com/benchmarks>


## Cross-platform

ASP.NET was built on the legacy **System.Web** library and rely on Windows IIS to provide server hosting. This symbiotic relationship prevented this application from being cross-platform and made it mandatory to create a different hosting model.

.NET Core applications are self-host a web server that handles the request directly, instead of letting IIS calling directly specific points of the application. An example is Kestrel, a fast, cross-platform HTTP server.


## The kestrel

Be default, every ASP.NET Core application has Kestrel as a built-in web server responsible for receiving raw requests and constructing an internal representation of the data. The resulting _HttpContext_ object will then be available to the rest of the application.

![](</images/demystifying_aspnet_core/4.png>)

ASP.NET Application hosting models

![](</images/demystifying_aspnet_core/5.png>)

ASP.NET Core Application hosting models


# HTTP web request and reverse proxy

When a user navigates to a URL and sends a request using the HTTP protocol to the server, the request is captured by a **reverse-proxy** (IIS for windows, NGINX or Apache for macOS and Linux) and then forwarded to the ASP.NET Core application.

Because the only component exposed to the internet is the reverse-proxy, while the underlining web servers are exposed only to the proxy, this results in improved security and performance. Another advantage of a reverse proxy is decoupling the application from the underlining operative system.

![](</images/demystifying_aspnet_core/6.png>)


# A new CLR

.NET Core comes with a new runtime for application execution called **CoreCLR**. It follows the same layout and architecture of .NET CLR and does things like loading the IL code, compiling to machine-level, and collecting garbage.  
   
However, the CoreCLR doesn’t support features that are too specific for the Windows platform or are proved to be unnecessary like application domains and code access security.

![](</images/demystifying_aspnet_core/7.png>)


# One library to rule them all

While the .NET platform was originally forked to fit the single platforms’ needs, this lack of a unified class library made it hard for developers to share code between multiple .NET platforms. .NET Standard solves the code sharing problem by defining a uniform set of Base Class Libraries (BCL) APIs available to all .NET implementations independent of workload. This also enables developers to produce portable libraries that are usable across .NET implementations, using this same set of APIs.

![](</images/demystifying_aspnet_core/8.gif>)


# Middleware

A middleware consists of small components that execute **in sequence** when the application receives an HTTP request. It’s important to understand that Middleware can only use objects that are created by previous Middleware in the pipeline. For example, you can perform authorization only after the authorization middleware.

![](</images/demystifying_aspnet_core/9.gif>)
