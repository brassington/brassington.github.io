---
layout: post
title: 'Reducing the Feedback Loop'
description: 'Using our tooling to reduce feedback loop'
category: Rust
tags: [Code]
image:
  feature: 
comments: true
share: false
---

As developers, it's easy to get swept up in the hype surrounding a new tool, and new language, a new way of thinking about a concept, or programming pattern. Sometimes we spend a lot of time reinventing the wheel over and over and over again. It would seem to me that some of the adjustments I've made in my career that have paid off in spades are the ones I've used systematically through adjusting my tooling.

I've picked up Vim and Emacs, and still regularly use a combo of the too (EVIL bindings on EMACs in [Spacemacs](http://spacemacs.org/)). Lately, I've been using VSCode, a tool developed my Microsoft on top of Electron (used for Atom, another text editor based on web tech). It's surpisingly responsive, and has some great features.

When I started developing, I started by learning C#, a Microsoft .Net language that was the 'me too' answer to Java on Windows only. Alot of that is changing now, but one thing I've always missed in the JS world is the tooling that surrounded Visual Studio in the form of autocompletion, hinting, and validation. Now some developers consider these a crutch, and I suppose they can be if they're overdepended on, but they also can be a fantastic way to learn a language, enabling new avenues for exploring programmatic APIs.

I've been working toward becoming proficient in Rust, and it has been a bit of an uphill climb. Even though the resources are plentiful and well written, sometimes it takes a lot of discipline to continue compiling repeatedly, keeping the conversation with the compiler going. It can feel like work. Tooling, like what Microsoft has provided for a decade in .NET can help with this!

You'd be suprised how much the developer experience can improve with a tighter feedback loop with ahead-of-time compiled languages like Rust. Now I really feel like I'm having more of a conversation, as the compiler inserts hints, underlining the parts of my code that will error on compilation.

All of this is thanks to the [Rust Language Server](http://www.jonathanturner.org/2017/01/rls-alpha-release.html), now in an Alpha release. I feel like I'm having more fun learning Rust because I know I can fix something as soon as I've typed it instead of switching contexts again five minutes later when I try to compile. This is an excellent improvement in tooling, and I'm really excited to see more languages adopt the language server protocols in the future. 

<iframe src="//giphy.com/embed/TUn7NE28BYY0w" width="480" height="318" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

Interested in checking this out for yourself? Go clone down the [rls_vscode](https://github.com/jonathandturner/rls_vscode) repo from github and install the rest of the Rust tooling referred to [here](http://www.jonathanturner.org/2017/01/rls-alpha-release.html).

What do you think? Let me know in the comments or reach out to me at andrew@brassington.io
