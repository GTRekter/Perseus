---
layout: post
title: "What is GAC?"
subtitle: ""
description: "The Global Assembly Cache (GAC) is a local cache maintained by the .NET Framework where strong-named assemblies (their name is composed by: name of assembly, version number, culture and public key token) that are designated to be shared by all the applications executed on a system will be stored..."
excerpt: ""
date: 2020-06-18T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["CSharp", "Dotnet", "Web Development", "Software Development"]
URL: "/2020/06/18/what_is_gac/"
categories: [ Tech ]
---

The Global Assembly Cache (GAC) is a local cache maintained by the .NET Framework where strong-named assemblies (their name is composed by: name of assembly, version number, culture and public key token) that are designated to be shared by all the applications executed on a system will be stored.

The concept of GAC is the result of the .NET architecture which design solves the issue of DLL hell that existed in COM (Component Object Model).


# What’s DLL Hell?

DLL Hell refers to a set of problems caused when multiple applications attempt to share a common dynamic link library (DLL). With COMs, when an application tried to install a DLL which was already stored in the registry, the system let prevail the latest version by overriding the oldest with the newest. Since DLLs doesn’t have any built-in mechanism for backwards comparability, this override caused the existing applications that rely on the shared DLL to crash. By strongly name each dynamic link library (.dll), the GAC solved this problem.


# Examples

A scenario where DLL hell occurs is:

1. On your machine there is an application which use an assembly called SharedAssembly.dll
2. I install a new application which install a new version of SharedAssembly.dll causing the replacement of the existing DLL
3. The first application stop working properly due to the newer version of SharedAssembly.dll.

![](</images/what_is_gac/images/1.png>)

# GAC Tool (gacutil.exe)

The Global Assembly Cache tool is a tool automatically installed with Visual Studio which allows a developer to check for assembly existence, view and manipulate the contents of the global assembly cache.

To run the tool, open the Developer Command Prompt for Visual Studio as Administrator and execute the command

```bash
    gacutil
```

Immediately a helper with all the available flags and their related descriptions will be prompted.

```bash
    **********************************************************************
    ** Visual Studio 2019 Developer Command Prompt v16.2.5
    ** Copyright (c) 2019 Microsoft Corporation
    **********************************************************************
    C:\Windows\System32>gacutil
    Microsoft (R) .NET Global Assembly Cache Utility.  Version 4.0.30319.0
    Copyright (c) Microsoft Corporation.  All rights reserved.
    Usage: Gacutil <command> [ <options> ]
    Commands:
     /i <assembly_path> [ /r <...> ] [ /f ]
       Installs an assembly to the global assembly cache. <assembly_path> is the
       name of the file that contains the assembly manifest.
       Example: /i myDll.dll /r FILEPATH c:\projects\myapp.exe "My App"
     /il <assembly_path_list_file> [ /r <...> ] [ /f ]
       Installs one or more assemblies to the global assembly cache.
       <assembly_list_file> is the path to a text file that contains a list of
       assembly manifest file paths. Individual paths in the text file must be
       separated by CR/LF.
       Example: /il MyAssemblyList.txt /r FILEPATH c:\projects\myapp.exe "My App"
         myAssemblyList.txt content:
         myAsm1.dll
         myAsm2.dll
     /u <assembly_display_name> [ /r <...> ]
       Uninstalls an assembly. <assembly_name> is the name of the assembly
       (partial or fully qualified) to remove from the Global Assembly Cache.
       If a partial name is specified all matching assemblies will be uninstalled.
       Example:
         /u myDll,Version=1.1.0.0,Culture=en,PublicKeyToken=874e23ab874e23ab
         /r FILEPATH c:\projects\myapp.exe "My App"
     /uf <assembly_name>
       Forces uninstall of an assembly by removing all traced references.
       <assembly_name> is the full name of the assembly to remove.
       Assembly will be removed unless referenced by Windows Installer.
       !! Warning: use the /uf command with care as applications may fail to run !!
       Example: /uf myDll,Version=1.1.0.0,Culture=en,PublicKeyToken=874e23ab874e23ab
     /ul <assembly_display_name_list_file> [ /r <...> ]
       Uninstalls one or more assemblies from the global assembly cache.
       <assembly_list_file> is the path to a text file that contains a list of
       assembly names. Individual names in the text file must be
       separated by CR/LF.
       Example: /ul myAssemblyList.txt/r FILEPATH c:\projects\myapp.exe "My App"
         myAssemblyList.txt content:
         myDll,Version=1.1.0.0,Culture=en,PublicKeyToken=874e23ab874e23ab
         myDll2,Version=1.1.0.0,Culture=en,PublicKeyToken=874e23ab874e23ab
     /l [ <assembly_name> ]
       Lists the contents of the global assembly cache. When the optional
       <assembly_name> parameter is specified only matching assemblies are listed.
     /lr [ <assembly_name> ]
       Lists the contents of the global assembly cache including traced reference
       information. When the optional <assembly_name> parameter is specified only
       matching assemblies are listed.
     /cdl
       Deletes the contents of the download cache
     /ldl
       Lists the contents of the download cache
     /?
       Displays a detailed help screen
    Old command syntax:
     /if <assembly_path>
       equivalent to /i <assembly_path> /f
     /ir <assembly_path> <reference_scheme> <reference_id> <description>
       equivalent to /i <assembly_path> /r <...>
     /ur <assembly_name> <reference_scheme> <reference_id> <description>
       equivalent to /u <assembly_path> /r <...>
    Options:
     /r <reference_scheme> <reference_id> <description>
       Specifies a traced reference to install (/i, /il) or uninstall (/u, /ul).
         <reference_scheme> is the type of the reference being added
           (UNINSTALL_KEY, FILEPATH or OPAQUE).
         <reference_id> is the identifier of the referencing application,
           depending on the <reference_scheme>
         <description> is a friendly description of the referencing application.
       Example: /r FILEPATH c:\projects\myapp.exe "My App"
     /f
       Forces reinstall of an assembly regardless of any existing assembly with
       the same assembly name.
     /nologo
       Suppresses display of the logo banner
     /silent
       Suppresses display of all output
```
