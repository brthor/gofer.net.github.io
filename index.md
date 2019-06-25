---
layout: default
title: Home
nav_order: 1
description: "Gofer.NET is an easy C# API for distributed background tasks/jobs for .NET Core."
permalink: /
---

# Easy distributed background tasks/jobs for .NET Core.

Use Gofer.NET to run background jobs (one-off, scheduled, or recurring) on a worker pool easily.

===============

## What is this?

Inspired by celery for python, this is a distributed job runner for .NET Standard 2.0 Applications.

- Use natural expression syntax to queue jobs for execution.

- Scale your worker pool by simply adding new nodes.

- Backed by Redis, all tasks are persistent.

## Getting Started

#### Install the dotnet cli

We recommend using the (dotnet cli)[https://dotnet.microsoft.com/download] to get started, but it's not a necessity. 

The dotnet cli is part of the (.NET Core SDK)[https://dotnet.microsoft.com/download].

#### Start a Redis instance.

We recommend using (docker)[] to start a local Redis instance for testing. Setting up a production-level Redis instance is out of the scope of this guide.

```bash
$ docker run -d -p 127.0.0.1:6379:6379 redis:4-alpine
```

#### Create a project.

Open up a terminal and create a new console project to get started.

```bash
$ mkdir myProject && cd myProject
$ dotnet new console
```

#### Add the Gofer.NET NuGet package.

```bash
$ dotnet add package Gofer.NET --version 1.0.0-*
```

#### Queue up some jobs.

This example `Program.cs` shows how to queue jobs for the worker pool to process, then start a worker to go and run them. 

Some important notes:
 - Workers would usually be separate from the code queueing the jobs, this is purely to give an example.

 - More workers can be added at any time, and will start picking up jobs off the queue immediately.

```c#
public class Program
{
    public static async Task Main(string[] args)
    {
        var redisConnectionString = "127.0.0.1:6379";
        
        // Create a Task Client connected to Redis
        var taskClient = new TaskClient(TaskQueue.Redis(redisConnectionString));
        
        // Queue up a Sample Job
        await taskClient.TaskQueue.Enqueue(() => SampleJobFunction("Hello World!"));
        
        // Start the task listener, effectively turning this node into a worker.
        await taskClient.Listen();
    }
    
    private static void SampleJobFunction(object value)
    {
        Console.WriteLine(value.ToString());
    }
}
```
