---
layout: post
title: 'Keep Chipping Away'
description: '5 Key Strategies to Reduce SPA Script Load Times'
category: Web
tags: [Web, Performance]
image:
  feature: 
comments: true
share: false
---

I wrote this blog post to go along with a lightning talk given at an internal Microsoft performance conference on May 20, 2019.

When attempting to tackle performance improvements in a legacy JS single page application, it can be difficult to know where to start. First and foremost, I want to encourage you to keep chipping away. Profile, profile, profile with Chrome Performance tab in the developer tools. Also add performance events liberally to help you identify bottlenecks in the bootup and runtime performance of your application code.

At Microsoft, I've worked for 2 years on improving load times for Yammer. We're far from where we want to be, but we've made a lot of progress. In 2017 when I started working on some of these issues, we were at ~8.5s home (P50) feed load time, the first feed with primary content users see by default when loading Yammer for the first time. Now, we're consistently hovering around 3.7s (P50), total home feed load time, more than half the original load time!

There are many improvements which have contributed to these improvements, including a long and difficult migration to Azure for all backend services, as well as our asset serving pipeline. But one of the largest contributors has been optimization work we've done to reduce Javascript static asset sizes.

The initial effort involved updating our version of Webpack to the latest supporting dynamic `import`. Converting all our routes to use dynamic `import` immediately cut ~30% off our 5.55MB total script payload, bringing us down to 3.9MB total. As you can see in the following Gif, this had an immediate effect on reducing feed load times to around 6 seconds.

<img src="https://im2.ezgif.com/tmp/ezgif-2-829b09c1d6e1.gif">

Take a look at [Matt Zeunert's how-to post](http://www.mattzeunert.com/2016/12/22/vs-code-time-travel-debugging.html) to get this setup in your environment to try it out.

I immediately asked the question, how is this working? My initial guess is that like the React and Clojure ecosystem, a data structure like a Trie might be involved in storing the differences in state of the world at every line of execution.

Seems that it's not quite so simple as a single data structre to manage and optimize the recorded state. Basically the approach uses a combination of recording snapshots of the execution state periodically, but also adding a logging mechanism to replay execurtions between snapshots.

The original research was done by [Barr and Marron who build Tardis](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/TimeTravelDbg.pdf) (the name for the time travelling work) into the CLR for C#/.NET environments

>A standard approach for implementing a TTD system is
>to take snapshots of a program’s state at regular intervals
>and record all non-deterministic environmental interactions,
>such as console I/O or timer events, that occur between snapshots.
>Under this scheme, reverse execution first restores the
>nearest snapshot that precedes a reverse execution target,
>then re-executes forward from that snapshot, replaying environmental
>interactions with environmental writes rendered
>idempotent, to reach the target.

The optimizations are where things get interesting, they piggyback on the CLR Garbage Collector process to store the Heap/Stack execution environment.

JS is also Garbage Collected, so I wondered if they used the same approach for the Node-ChakraCore version?

Check out the work on the Chakra-Node version in this paper, worked on by [Barr, Marron, Maurer, Moseley and Seth](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/09/TTNode.pdf)

I couldn't find a clear answer to that question, but it seems they used the eventloop to determine when to take snapshots of the environment, basically analyzing the queue and only taking snapshots when items are on the queue scheduled for execution. 

Super cool stuff! Can't wait to use this in my day-to-day workflow, seems like a great tool to have once it's had more of its bugs shaken out!

What do you think? Let me know in the comments or reach out to me at andrew.brassington@microsoft.com
