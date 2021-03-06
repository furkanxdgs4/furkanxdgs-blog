---
title: A RenderGraph's Purpose
subtitle: Developing a RenderGraph is complicated profession!
date: 2020-12-20T14:49:50.443Z
draft: false
featured: false
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
Modern APIs give lots of responsibilities to the user and this generally makes it harder to debug and manage data-flow in applications. RenderGraph abstraction is a helper here, so here's my thoughts on how a RenderGraph should be developed:

1. Main purpose is to make it easier to use these APIs in a more efficient way. Complexities of the GPU hardware is extremely increasing and differences between generations is getting larger which causes application developers to choose a side to get best performance from. 
2. RenderGraph's to get best performance from all hardware by making the renderer developing process better structured and showing the higher picture of the all rendering proccesses. Also, complexities of the game worlds're increasing each day which makes debugging progresses more complicated. 
3. RenderGraph's to make all of the GPU operations easier to debug as much as possible. Visualization of dataflow is always a plus and visualizing all of the API calls is a need in modern APIs (Crash reporting!)

Old APIs (OpenGL, Direct11 etc.) is replaced because of the driver complexities (API devs are humans too!) and low CPU performance (and also possible GPU non-utilizations) because of the complicated resource and operation dependency solvations. Modern APIs gives these dependency solvation proccess to app developer's hands which ends up being hell of a work done by app developers even for basic operations. 

I took this point from my RenderGraph investigation: Users should categorize their operations by their operation type (G-Buffer, Compute Lighting, Post-Proccessing etc) and set dependencies on each other first. Then divide these proccesses in smaller parts by their interest on operation execution order (draw call execution order etc), operation type etc. Then should give some data about how each of these should run. The data should be prepared in 2 ways to get best performance/manuality (the amount of data given by app developer per operation) ratio:

1. You prefer performance over manuality and this doesn't allow you do some complex operations like; while G-Buffering, some of your objects're writing to some other buffers and this needs synchronization (or some "smart" scheduling of operations that won't allow any data-race) but you don't allow to that and instead add new maximum in-frame synchronization barrier count of SubDrawPasses (Vulkan's Subpass concept). That means, you have to know worst case scenarios and be prepared for it. Culling unnecessary jobs should be RenderGraph's job.
2. You prefer freedom and "some" performance loss against Way 1 -but again, better performance than old APIs- in the cost of pre-processing and some possible CPU performance loss.

Yeah, it seems very obvious that Way 2 is the way to go but it is not that easy to do so. Because what do I mean by pre-processing, what are we pre-processing? I don't know AI but running an hour of different benchmark scenes and gathering some informations about how the hardware will run the application is the way to go? But shouldn't this be the job of the app developer? Like I said, because I don't know it I can't say anything about it. 

What I think is GFX API should benchmark the hardware in a more basic way and some sort of "guess" -in an algorithmic way, rather than a deep learning- which operation is gonna finish first and decide how should synchronizations be placed. With this lack of information, this "guess" can't be implemented. If you think about it, this is how old APIs handles everything but GFX API is gonna be working on way less combination of cases (because you are obliged to define a RenderGraph anyway). But Way 2 results in a worse case, it is gonna be hard to understand what it does. Also this type of APIs that handles something in a "magical" way should handle everything in a magical way rather than just a subset of it, otherwise there'll be lots of wrong-usage related bugs which are hard to debug.\
So my way to go is Way 1 for now, because I still can't even think about a clearer (handles everything in a "magical" way) approach than Way 2. The clearer way I think is something like an old API, which is already exists and which is way beyond me.