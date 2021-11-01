---
title: ".Net Heap and Stack"
categories:
  - dotnet
tags:
  - dotnet
  - heap
  - stack
---

There are two places the .NET framework stores items in memory as your code executes : the Heap and the Stack.

- The Stack is self managed.
- The Heap is managed by the Garbage collector.
- Value Types are stored in the stack
- Reference Types are stored into the Heap. A pointer to this reference value is stored in the Stack.

The garbage collector does scan the stack to see what things in the heap are currently being used (pointed to) by things on the stack.

It makes no sense for the garbage collector to consider collecting stack memory because the stack is not managed that way: Everything on the stack is considered to be "in use." And memory used by the stack is automatically reclaimed when you return from method calls. Memory management of stack space is so simple, cheap and easy that you wouldn't want garbage collection to be involved.

In .NET, we typically allocate all local variables in Stack.
the Heap is used to store objects (references to those objects are stored in Stack)
and global variables, static global variables and types (when we use new keyword).

Each thread has its Stack. However, Heap is consistently shared among all the threads.

For example, a 32-bit application generally has a virtual address space of 2GB. This means that if the stack size is 2MB (as default in pthreads), then you can create a maximum of 1024 threads. This can be small for applications such as web servers. Increasing the stack size to, say, 100MB (i.e., you reserve 100MB, but do not necessarily allocated 100MB to the stack immediately), would limit the number of threads to about 20, which can be limiting even for simple GUI applications.

32-bit processors are perfectly capable of handling a limited amount of RAM (in Windows, 4GB or less), and 64-bit processors can utilize much more.
A 32-bit system can access 2exp32 memory addresses (4 GB of RAM)

Given a memory of 2GB => 2 \* 1024MB= 2048MB and a stack of 2MB
=> max threads= 2048 / 2 = 1024 threads
Each thread shares the heap memory and have it's stack

Garbage Collector Generations — based on an object’s life cycle, there are three generations (categories):

- Generation 0 — newly created object is put in Generation 0 and has not been checked by Garbage Collector yet.
- Generation 1 — object inspected by Garbage Collector once but kept in Generation 1 because having a root reference.
- Generation 2 — if the object passes two or more inspections and is not terminated by Garbage Collector because of having a root reference are in Generation 2.

The stack is much faster than the heap.
This is because of the way that memory is allocated on the stack.
Allocating memory on the stack is as simple as moving the stack pointer up.

Virtual Memory:
Let’s say that an operating system needs 120 MB of memory in order to hold
all the running programs, but there’s currently only 50 MB of available physical memory
stored on the RAM chips.
The operating system will then set up 120 MB of virtual memory, and will use
a program called the virtual memory manager (VMM) to manage that 120 MB.

The VMM will create a file (paging file) on the hard disk that is 70 MB (120 – 50) in size
to account for the extra memory that’s needed.

The OS will now proceed to address memory as if there were actually 120 MB
of real memory stored on the RAM, even though there’s really only 50 MB.

Whenever the OS needs a ‘block’ of memory that’s not in the real (RAM) memory,
the VMM takes a block from the real memory that hasn’t been used recently,
writes it to the paging file, and then reads the block of memory that
the OS needs from the paging file. The VMM then takes the block of memory
from the paging file, and moves it into the real memory – in place of the old block.

This process is called swapping (also known as paging), and the blocks of memory
that are swapped are called pages.

There are two reasons why one would want this: the first is to allow the use of programs
that are too big to physically fit in memory.
The other reason is to allow for multitasking – multiple programs running at once.

- stack is a linear data structure whereas Heap is a hierarchical data structure.
- Stack memory will never become fragmented whereas Heap memory can become fragmented as blocks of memory are first allocated and then freed.
- Stack accesses local variables only while Heap allows you to access variables globally.
- Stack variables can’t be resized whereas Heap variables can be resized.
- Stack memory is allocated in a contiguous block whereas Heap memory is allocated in any random order.
- Stack doesn’t require to de-allocate variables whereas in Heap de-allocation is needed.
- Stack allocation and deallocation are done by compiler instructions whereas Heap allocation and - deallocation is done by the programmer.

## Links

<https://github.com/Maoni0/mem-doc/blob/master/doc/.NETMemoryPerformanceAnalysis.md>
<https://devblogs.microsoft.com/dotnet/author/maoni/page/4/>/>
<https://www.red-gate.com/simple-talk/development/dotnet-development/net-memory-management-basics/>
<https://www.red-gate.com/products/dotnet-development/ants-memory-profiler/learning-memory-management/memory-management-fundamentals>
<https://dev.to/tyrrrz/interview-question-heap-vs-stack-c-5aae>
<https://www.fullstack.cafe/blog/heap-interview-questions>
<https://www.codingame.com/playgrounds/6179/garbage-collection-and-c>

<https://medium.com/swlh/optimizing-garbage-collection-in-a-high-load-net-web-service-3bb620b444a7>
<https://medium.com/criteo-engineering/build-your-own-net-memory-profiler-in-c-allocations-1-2-9c9f0c86cefd>
<https://www.jetbrains.com/help/dotmemory/Analysis_Overview_Page.html#timeline>
<https://www.codejourney.net/2018/08/net-internals-06-generational-garbage-collection/>
<https://www.codingame.com/playgrounds/6179/garbage-collection-and-c>
<https://www.dynatrace.com/news/blog/net-performance-analysis-a-net-garbage-collection-mystery/>
<https://www.codejourney.net/2018/08/net-internals-06-generational-garbage-collection/>
