---
date: 2019-08-17 17:00:49
layout: post
title: "Graphics Pipeline Overview"
subtitle: 
description: This post gives a gentle introduction to rasterization, the  most common algorithm for displaying 3D data on a 2D screen.
image: /assets/img/Gfx_Pipeline/graphics_background_1.jpg
optimized_image:
category: graphics
tags:
author: Agent 007
paginate: false
---

We are almost constantly manipulating the pixels on our screens. Be it watching movies, playing video games, or simply reading texts, pixels are continuously switching between millions of states (colours) every second as we use our computers.

But how does a computer find out the state of all the pixels for generating an image ?
How does it know that one configuration results in the [Eiffel Tower](/assets/img/Gfx_Pipeline/Eiffel_Tower.jpg) while another results in the [Pyramid of Giza](/assets/img/Gfx_Pipeline/Giza_Pyramid.jpg) ?

In this post, we are going to understand how our GPUs convert 3D images/ scenes into 2D images on the screen.
We are going to follow the journey of a 3D image (made of vertices) through various stages of processing, and conclude with the resulting 2D representation (made of our screens' pixels).

## How do we talk to the GPU ?

3D scene information is fed to the GPU, consisting of streams of hundreds of vertices. The CPU is responsible for allocating memory, initializating variables, and the formatting of data. Once this is done, the data is transferred to the allocated GPU memory (aka **frame buffer**).<br>
Once it has all the data in its buffer, the GPU takes over and runs the graphics pipeline, generating a 2D scene which is then **directly sent to the display adapter** without CPU intervention.
The most popular display adapters are
- [VGA](https://en.wikipedia.org/wiki/Video_Graphics_Array)
- [HDMI](https://en.wikipedia.org/wiki/HDMI)
- [DVI](https://en.wikipedia.org/wiki/Digital_Visual_Interface)

## Graphics APIs 

Due to the sheer complexity of most modern 3D applications (read AAA games), the use of APIs (Application Programming Interfaces) has become ubiquitous for al kinds of graphics programming. The primary benefits of the APIs is that they abstract away a significantamount of unnecessary detail away from the hardware while still enabling programmers to control the graphics operations on the GPU. Someof the most well known APIs are [*OpenGL*](https://www.opengl.org/), [*Vulkan*](https://www.khronos.org/vulkan/), [*DirectX 11 (DX 11)*](https://docs.microsoft.com/en-us/windows/win32/direct3d11/atoc-dx-graphics-direct3d-11), 
[*DirectX 12 (DX 12)*](https://docs.microsoft.com/en-us/windows/win32/direct3d12/direct3d-12-graphics) , and 
[*Metal*](https://developer.apple.com/metal/).

All APIs have specialized functions to query and manipulate the state of the GPU. While each API may have differing implementations, and somelike Vulkan and DX-12 are very low level, they all provide a way of querying the essential information such as:
- Is the GPU active ?
- PCI port number
- Memory available 
- Any exceptions encountered, along with a typical "GPU stack" trace<br>

All of which are crucial for setup, debugging, and diagnostic operations while writing graphics applications. With a fair idea of how to communicate to the GPU, let us now turn our attention to the graphics pipeline.

## Graphics Pipeline

Before going any further - an important assumption made is that we're dealing with 3D scene coordinates that have not been already *rendered*. The term *rendered* is loosely used to describe any information which is already in form of pixels (such as a picture file on your computer which does not need to be converted from vertices to pixels).

The figure below shows the raster graphics pipeline.

![Standard Graphics Pipeline](/assets/img/Gfx_Pipeline/pipeline.JPG "Fig 1. The Standard Graphics Pipeline")

To get a better idea, let us equate the the functionality of the rasterizer to that of a camera that is capturing the external world onto 2D film.

### I. Vertex Generation

Any picture must mandatorily have an **object** being shown. It would indeed be a rather disconcerting revelation if the millions of photographs taken suddenly go blank with absolutely nothing in them. Of course, while dealing with an actual camera, the Entire Earth and space are just waiting to be studied in all their glory.

Unfortunately, the raterizer begins with absolutely **nothing**. Think pre Big Bang nothing. No light, no universe, no existence, and mosty importantly, nothing to photograph and post online !! While this is rather depressing, the power of creation is bestowed in the hands of the divine programmer.

Moving on from the philosophy of quantum mechanics, the task of the programmer is to define exactly **what** needs to be drawn. 
And we are required to define it in all gory detail. The coordinates the vertices of the object must be specified in 3D space, with respect to an arbitrary origin. For the sake of simplicity, let us consider a 3D cuboid. 

#### TODO : Animation - Cuboid pops into existence

While it may seem like nothing, all the dimensions of the cuboid must be determined prior to creating the vertices.