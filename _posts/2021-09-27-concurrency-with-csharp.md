---
title: Concurrency with C#
tags:
  - Dotnet
  - C#
published: true
---

When you have a lot of process to do in your C# app, but if you do it sequentially it's take so long. So, you can do it concurrency, this is all you have to see.

<!--more-->

**TLDR;**

let's me show the code first!

```cs
using System;
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            var tasks = new List<Task>();

            for (int i = 1; i <= 100; i++)
            {
                int taskNumber = i;
                tasks.Add(Task.Run(() => DoWorkA(taskNumber)));
            }

            tasks.Add(Task.Run(() => DoWorkB()));
            tasks.Add(Task.Run(() => DoWorkC()));

            await Task.WhenAll(tasks);

            Console.WriteLine("All tasks complete!");

            // Do another work here!

            Console.ReadKey();
        }

        private static void DoWorkC()
        {
            Thread.Sleep(100);
            Console.WriteLine("Task C Complete");
        }

        private static void DoWorkB()
        {
            Thread.Sleep(500);
            Console.WriteLine("Task B Complete");
        }

        private static void DoWorkA(int taskNumber)
        {
            Thread.Sleep(1000);
            Console.WriteLine($"Task A-{taskNumber} Complete");
        }
    }
}
```

Let's me explain, First assume you have 3 tasks to do and you want to do it concurrency, So i create a list to store all tasks. 

```cs
var tasks = new List<Task>();

for (int i = 1; i <= 10; i++)
{
    int taskNumber = i;
    tasks.Add(Task.Run(() => DoWorkA(taskNumber)));
}

tasks.Add(Task.Run(() => DoWorkB()));
tasks.Add(Task.Run(() => DoWorkC()));
```

In `DoWorkA()` wrap with loop to do run 10 times with difference arguments and then `DoWorkB()` and `DoWorkC()` so when a task added to `List<Task>()` task already run. 

To capture to all the tasks are complete is `await Task.WhenAll(tasks);` then after all task done you can go with synchronous code after that.

See result 

![](assets/images/posts/2021-09-27_10-59-16.png)