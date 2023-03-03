---
title: "What is a garbage collector?"
date: 2020-04-09T02:09:16+09:00
description: "The garbage collector (GC) is a part of the .NET framework which is initialized by the common language run-time (CLR) to manage the allocation and release of memory in an application..."
tags: ["CSharp", "Dotnet", "Web Development", "Software Development"]
thumbnail: /what_is_a_garbage_collector/0.png
---

The garbage collector (GC) is a part of the .NET framework which is initialized by the common language run-time (CLR) to manage the allocation and release of memory in an application.


# Types of garbage collector

The garbage collector can work in a wide variety of scenarios. The CLR provides the following types of garbage collection:

- **Workstation garbage collection (GC)** runs on the normal priority thread, can be concurrent or non-concurrent, and is designed for client apps
- **Server garbage collection** is designed for server applications and creates a separate managed heap and a corresponding garbage collection thread for each logical CPU. Threads run on highest priority making it faster but more resource intensive. In .NET Core, .NET Framework 4.5 and later versions server garbage collection can be non-concurrent or background. In .NET Framework 4 and previous versions, server garbage collection is non-concurrent.


# Concurrent garbage collection

Concurrent garbage collection allows user threads to run for the most of generation 2 garbage collection. As long as there is still free space in the managed heap for new allocations, user threads are allowed to run and create new objects.

![](</what_is_a_garbage_collector/1.png>)


# Non-concurrent garbage collection

Non-concurrent garbage collection suspends all non-garbage-collection threads for the full duration of the garbage collection, effectively pausing the application for that time.


# Background garbage collection

Background garbage collection is the replacement for concurrent garbage collection. It is similar to concurrent garbage collection but allows generation 0 and generation 1 garbage collection to interrupt an ongoing generation 2 garbage collection and temporarily block program execution. After generation 0 and generation 1 garbage collection is completed, both the program execution as well as the generation 2 garbage collection, continues. This even further shortens the time the program execution is paused because of garbage collection.


# Memory and managed heap

Once the garbage collector is initialized by the common language runtime (CLR), it allocates a segment of memory called managed heap which opposed to a native heap in the operating system, to store and manage objects.

For each managed process, the garbage collector reserve a segment of memory in the managed heap using the Windows VirtualAlloc function. (It uses the Windows VirtualFree function to release segments back to the OS). All threads in the process allocate memory for objects on the same heap.

One important thing to consider is that the size of each allocated segment is implementation-specific and it’s subject to change at any time.


# Generations

The objects stores into the managed heap are organized into generations according their age. Garbage collection primarily occurs with the reclamation of short-lived objects that typically occupy only a small part of the heap.

1. **Generation 0**: Youngest generation with short-lived object (ex. temporary variable or newly allocated objects unless they are large objects, in that case they go on the large object heap in a generation 2 collection)
2. **Generation 1**: If space occupied by some generation 0 objects that are not released in a garbage collection run, then these objects get moved to generation 1 and contains short-lived object.

- **Generation 2:** If space occupied by some generation 1 objects that are not released in the next garbage collection run, then these objects get moved to generation 2. This generation contains long-lived objects (i.e. object in a server application that contains static data that’s live for the duration of the process).

When the garbage collector detects that the survival rate is high in a generation, it increases the threshold of allocations for that generation.


# Garbage Collector phases

The collection is triggered by one of the following conditions:

- The system has low physical memory. This is detected by either the low memory notification from the OS or low memory as indicated by the host.
- The memory that is used by allocated objects on the managed heap surpasses an acceptable threshold. This threshold is continuously adjusted as the process runs.
- The **GC.Collect** method is called. In almost all cases, you do not have to call this method, because the garbage collector runs continuously.

At this point the garbage collector passes throw the following phases:

1. **Marking Phase**: The GC create a list of all the live objects (all of the objects that are not on the list are potentially deleted) by using the following information the to determine whether objects are live or not:

- Stack roots. Stack variables provided by the just-in-time (JIT) compiler and stack walker. JIT optimizations can lengthen or shorten regions of code within which stack variables are reported to the garbage collector.
- Garbage collection handles. Handles that point to managed objects and that can be allocated by user code or by the common language run-time.
- Static data. Static objects in application domains that could be referencing other objects. Each application domain keeps track of its static objects.

1. **Relocating Phase**: The references of all the objects that were on the list of all the live objects are updated in the relocating phase so that they point to the new location where the objects will be relocated to in the compacting phase.
2. **Compacting Phase**: reclaims the space occupied by the dead objects and compacts the surviving objects. The compacting phase moves objects that have survived a garbage collection toward the older end of the segment. The heap gets compacted in the compacting phase as the space occupied by the dead objects is released and the live objects remaining are moved. All the live objects that remain after the garbage collection are moved towards the older end of the heap memory in their original order


# Resources

- Official documentation: <https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals>
