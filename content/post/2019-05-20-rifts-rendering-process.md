---
title: RIFT's Rendering Process
date: 2019-05-20T16:59:57.069Z
summary: How them pretty pixels get made.
keywords:
  - coding
  - c++
  - RIFT
categories:
  - Coding Thoughts
  - Rift
tags:
  - rift_renderer
showDate: true
showTags: true
showPagination: true
showSocial: true
showMeta: true
showActions: true
---
One of the goal I had in starting this blog was documenting the thought process and choices behind the structure of RIFT.  Probably best to start that with the linchpin for the whole system: **the rendering process**.

## Decision: Monolithic or Modular?

RIFT is taking some inspiration from appleseed in that the renderer itself is a shared library module that is interacted with by a separate 'front end' executable.  So far that front end is a simple commandl line based application (RIFT_cli).  One of my first big questions was what I wanted that executable to be doing.  Was it a simple interface that takes a list of commands, passes them off to the renderer, and then basically calls it quits?  Or was it going to be more involved, like a central location for the entire process?

Ultimately, I decided on the latter.  The renderer would define and provide the necessary data structures, but it would be the reponsibility of the application to own that data and feed it not only to the render controller, but to any other post processing or output stages.

1. What does the main application own?
   1. The RenderSettings object (samples, resolution, thread count, lighting and sample integrators, etc...)
   2. The main Scene object (all objects, materials, textures and light objects)
   3. The Render Controller (oversees and controlls the rendering process)
   4. A TileBuffer object (stores a series of tiles that are the raw results of the render process.  All render views (AOVs) are stored in an unordered map of pixel buffers inside each Tile by view name)
2. What does the Render Controller own?
   1. pointers to the render settings, scene and tile buffer
   2. an RNG (random number generator) system for providing seed values to render threads
   3. a vector array of rendering threads
   4. a TilePool object (this sections out the render image into 'tiles' of pixels and stores the four corners of it)
3. What does a Render Thread own?
   1. a LightingIntegrator object
   2. an RNGSampler object
   3. A Tile to store final render results
   4. SampleAccumulators for each render view

## The Rendering Process:

First, the render settings are processed by the front end.  Depending on that, a Scene object is created either by parsing a scene description file, or by running a number of preset generator tools.

Next, a TileBuffer is created that will store the results of the rendering process.

Next, pointers to the settings, scene and tile buffer are fed to a render controller.  That controller will then spawn an RNG seed generator that will seed itself from the system clock.

Next, render threads are created for each available core and pointers to the settings, tile_buffer, tile_pool and scene.  Each thread is then launched, creating lighting and sampler objects based on the render settings and a vector array of sample accumulators.

A thread starts out by querying the controller's tile pool for available work.  If a tile is available, it will take that tile outline along with a seed value from the controller RNG.  The rendering process starts.  For each ray hit the HitRecord will be passed to each sample accumulator.  At the end of rendering each accumulator is placed by name reference into a Tile object.  That Tile object is then push_back'ed to the mutex locked TileBuffer.  The thread then queries the tile_pool again.  If work is available the process repeats, if not the thread exits.

After all this, the front end is left with a vector array of tiles, each tile holding the pixels for all the views (AOVs) in the image.  If those images need to be processed or exported, the fron end will deal with calling the appropriate exporter or converter class and passing the tile buffer into it.



## The challenges for me regarding this design:

The biggest challenge so far has been working within the restrictions of RIFT itself being a shared library.  I'll talk about some of the gotchas I've run across so far in another post, but suffice to say the front end and the render library reside in different spaces of memory, and passing items between them is not as straightforward as it seems.

In the meantime, here's some code to keep you occupied:
