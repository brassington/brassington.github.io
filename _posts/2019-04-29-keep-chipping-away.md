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

<img src="http://machineloop.github.io/assets/2017-performance-step.gif">

We've continued to improve since brining us to about ~723KB total script tranferred when compressed, down from 1.5MB compressed when we started the effort. Yammer has used these techniques to cut our load times in half, your team can too! The rest on this blog post (and the conference talk) focuses on five of the techniques we used to help Yammer load faster: 
  1. Preload and Prefetch to split and prioritize payloads
  2. Add stable asset fingerprints and infinite cache headers to improve client-side caching
  3. Use dynamic import techniques and lazy module wrapping utilities
  4. Ramp up compression with Brotli and Zopfli
  5. Add fine-grained performance budgets in webpack to control output chunk sizes
  
<br/>
# Prefetch & Preload
<br/>

> `<preload>`  =  highest priority
>
> `<prefetch>` =  lowest priority

Take a look at [Addy Osmani's excellent blogpost on script priorities](https://addyosmani.com/blog/script-priorities/) shows how scripts are prioritized by Chrome, outside of a script tag in the head, the preload link tag is highest priority, while prefetch is lowest priority (the browser decides when to fetch, if at all):

<style>
.priorities-table {
  color: #111;
  font-size: 1em;
}

.priorities-table td {
  border: 1px solid black;
}

.priorities-table .heading {
  font-weight: bold;
}

.priorities-table .veryhigh {
  background-color: #E0B9B1;
}

.priorities-table .high {
  background-color: #EECDCD;
}

.priorities-table .medium {
  background-color: #F3CCA2;
}

.priorities-table .low {
  background-color: #D2E2F1;
}

.priorities-table .lowest {
  background-color: #DBF2F1;
}
</style>
<table class="priorities-table">
  <tbody><tr>
   <td>
   </td>
   <td><strong>Loading priority<br>
(network/Blink)</strong>
   </td>
   <td><strong>Execution priority</strong>
   </td>
   <td><strong>Where should this be used?</strong>
   </td>
  </tr>
  
  <tr>
   <td class="heading"><code>&lt;link rel=preload&gt;</code> +<br>
<code>&lt;script async&gt;</code> hack
<p>
or 
</p><p>
<code>&lt;script type=module async&gt;</code>
   </p></td>
   <td class="medium">Medium/High
   </td>
   <td class="high">High -<br>
Interrupts parser
   </td>
   <td><ul>

<li>Scripts that generate critical content (needed for FMP) 
<ul>
 <li>But shouldn't affect above-the-fold layout of the page
</li><li>Scripts that trigger network fetches of dynamically inserted content
</li><li>Scripts that need to execute as soon as their imports are fetched, use <code>&lt;script async type=module&gt;</code>

<p>&nbsp;</p>
<strong>Examples:</strong><ul>

<li>Draw something on <code>&lt;canvas&gt;</code></li></ul>
</li></ul>

</li></ul>

   </td>
  </tr>
  
  
  
  
  <tr>
   <td><code>&lt;link rel=prefetch&gt;</code> + <code>&lt;script&gt;</code> in a next-page navigation
   </td>
   <td class="lowest">Idle / Lowest
   </td>
   <td class="medium">Depends on  when and how the script is consumed.
   </td>
   <td>Scripts very likely to provide important functionality to a next-page navigation.
<p>&nbsp;</p>
<strong>Examples:</strong><ul>

<li>JavaScript bundle for a future route</li></ul>

   </td>
  </tr>
</tbody></table>

<br/>
<br/>

If you don't already have your data bootstrap calls in a separate chunk from the rest of your application code, you can stratigically introduce a new code split point by adding a dynamic `import` where you call your bootstrap process. We used a `promise.all()` to start the bootstrap process, fetching initial user and network data, while loading the rest of the application code needed to display the bootstrapped service results.


When you start using dynamic `import` extensively, it is also helpful to tell the browser about all the chunks, this is what `preload` is particularly useful for, you can provide a manifest in the initial HTML payload which informs the browser about all chunks you may need to load in the future. The Browser will then treat those links as lowest priority, and only download them (or never download them) based on it's own heuristics (usually involving network speed and availability).


What do you think? Let me know in the comments or reach out to me at andrew.brassington@microsoft.com
