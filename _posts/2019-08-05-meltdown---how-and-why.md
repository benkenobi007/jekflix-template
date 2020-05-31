---
date: 2019-08-05 17:00:49
layout: post
title: "Meltdown - How and Why"
subtitle: 
description: This post is a high level overview of Meltdown. I attempt to explain the nuts and bolts of meltdown without diving into too much technical detail. Only a minimal knowledge of computers is required.
image: https://images.ctfassets.net/9ijwdiuuvngh/62ev5Y8JHisAAi0yOuIQKa/5c5e3850d98b8f841cecfc66930c81c4/BP_Meltdown-Spectre.png
optimized_image:
category: security
tags:
author: Agent 007
paginate: false
---

Let us start by exploring the key concepts required:
- Out of Order/ Speculative execution
- Virtual Memory and memory mapping
- Memory Protection


## Out of Order Execution
Whenever we run a program, we expect that the CPU executes the instructions line by line. Indeed, the entire field of computer software relies on this simplifying assumption of in-order execution by the CPU. From the perspective of the CPU though, this would result in an incredible waste of power and computation.

For example, let us say we want to bake a cake and prepare a salad. If we were to follow in-order execution, we would first have to wait for the cake to be baked, before we could even start collecting ingredients for the salad !

![in order execution flow](/assets/img/Meltdown/In_order_cropped.png "Fig 1. In-Order Execution")

From the figure, we can see that there are 4 steps needed to make the cake and salad.
- Prepare the cake batter
- Place the cake for baking
- Wait (upto an hour !!) for the cake to finish baking
- Prepare the salad

Isn't it a gross waste of time to sit twiddling our thumbs until the cake is baked ?<br>
Wouldn't we be able to make the salad while waiting for the cake to get baked ?<br>
Of course we would ! The key idea is that **the salad does not depend on the cake** in any manner whatsoever.

Let us now re-attempt the same excercise, but with Out of Order execution.

![out of order execution flow](/assets/img/Meltdown/out_order.png "Fig 2. Out-of-Order Execution")