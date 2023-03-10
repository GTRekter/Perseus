---
layout: post
title: ".NET Core deployment types"
subtitle: ""
description: "In this article, I will discuss the different deployment types available in .NET Core..."
excerpt: ""
date: 2020-08-12T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["CSharp", "Dotnet", "Web Development", "Software Development"]
URL: "/2020/08/12/dotnet_core_deployment_types/"
categories: [ Tech ]
---


In this article, I will discuss the different deployment types available in .NET Core.

There are two different types of deployments for .NET Core applications:

- Framework-dependent deployment (FDD)
- Self-contained deployment (SCD)


# Framework-dependent deployment

With the framework-dependent deployment, the application depends upon the .NET Core runtime installed on the target machine on which the application and third-party dependencies are deployed.

By default, when executing the `dotnet publish` command, the .NET Core CLI compiles and configure the application to target a specific version of .NET. It also publishes the resulting set of files using FDD to a directory. That targeted .NET Core runtime is required to be on any machine where your application runs.

```bash
    $ dotnet publish -f netcoreapp3.1 -c Release
```

# Minor version roll-forward

It’s important to mention that when an application runs from an FDD, the`dotnet` executable is the application’s **host**. It’s in charge of checking the presence of an acceptable latest patch version on the machine. If no acceptable runtime is installed, the application won’t run.

A few possible scenarios while deploying an application are:

- **Target version specified in the application**: 3.0  
  **Highest version installed in the target machine**: 3.0.3  
  **Version used to run the application**: 3.0.3
- **Target version specified in the application**: 3.0  
  **Highest version installed in the target machine**: 3.0.3 and no 3.0.\* versions are installed  
  **Version used to run the application**: 3.0.3
- **Target version specified in the application**: 3.0.  
  **Highest version installed in the target machine**: 2.1.1 and no 3.0.\* versions are installed  
  **Version used to run the application**: An error message is displayed
- **Target version specified in the application**: 2.0.  
  **Highest version installed in the target machine**: 3.0.0 and no 2.x versions are installed  
  **Version used to run the application**: An error message is displayed


# Pros

- The build only contains code and its external dependencies, which reduce its final size.
- There is no need to worry about the operating system installed on the target machine.
- As the .NET Core runtime is installed on server, the required disk space and memory cost is reduced.
- Thanks to the “Minor version roll-forward” mechanism, the FDD automatically applies the latest .NET Core security patch available on the system that runs the application.


# Cons

- The application need the correct version of the .NET Core runtime installed on the target machine.


# Self-contained deployment

When published using a self-contained deployment (SCD), the application doesn’t rely on the .NET Core runtime installed (or not installed) on the server. During the publishing, the .NET Core CLI creates a platform-specific executable that includes all required .NET Core files to run.

As .NET Core is cross-platform, it’s possible to specify e six different target platforms during the publishing:

- win-x86
- win-x64
- osx-x64
- linux-x64
- win-arm
- linux-arm

```bash
    $ dotnet publish --f netcoreapp3.1 --r osx.10.11-x64
```

# Pros

- The target machine doesn’t need to have .NET Core runtime installed.
- As the app is not using a shared .NET Core runtime, it’s possible to deploy multiple applications targeting different .NET Core versions in the same machine.
- Increase reliability by controlling which version of .NET Core used at runtime and when to upgrade each application.


# Cons

- The final build size is more significant than FDD because the SCD contains not only the app and its dependencies but also .NET Core libraries and .NET Core runtime.
- Publishing an application using SCD doesn’t roll forward to the latest available .NET Core security patch.


# References

- **Dotnet publish:** <https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-publish>
- **Framework dependent apps roll forward:**<https://docs.microsoft.com/en-us/dotnet/core/versions/selection#framework-dependent-apps-roll-forward>
