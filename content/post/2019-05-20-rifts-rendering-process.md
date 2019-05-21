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

RIFT is taking some inspiration from appleseed in that the renderer itself is a shared library module that is interacted with by a separate 'front end' executable.  So far that front end is a simple command line based application (RIFT_cli).  One of my first big questions was what I wanted that executable to be doing.  Was it a simple interface that takes a list of commands, passes them off to the renderer, and then basically calls it quits?  Or was it going to be more involved, like a central location for the entire process?

Ultimately, I decided on the latter.  The renderer would define and provide the necessary data structures, but it would be the responsibility of the application to own that data and feed it not only to the render controller, but to any other post processing or output stages.

1. What does the main application own?
   1. The **RenderSettings** object (samples, resolution, thread count, lighting and sample integrators, etc...)
   2. The main **Scene** object (all objects, materials, textures and light objects)
   3. The **RenderController** (oversees and controls the rendering process)
   4. A **TileBuffer** object (stores a series of tiles that are the raw results of the render process.  All render views (AOVs) are stored in an unordered map of pixel buffers inside each Tile by view name)
2. What does the Render Controller own?
   1. pointers to the render settings, scene and tile buffer
   2. an **RNGSampler** (random number generator) system for providing seed values to render threads
   3. a vector array of **RenderThreads**
   4. a **TilePool** object (this sections out the render image into 'tiles' of pixels and stores the four corners of it)
3. What does a Render Thread own?
   1. A **LightingIntegrator** object
   2. Pointers to the tile_pool, scene, and render settings
   3. An **RNGSampler** object for generating camera rays
   4. A **Tile** to store final render results
   5. **SampleAccumulators** for each render layer (AOV)

## The Rendering Process:

First, the render settings are processed by the front end.  Depending on that, a Scene object is created either by parsing a scene description file, or by running one of a number of preset generator tools.

Next, a TileBuffer is created that will store the results of the rendering process.

Next, pointers to the settings, scene and tile buffer are fed to a render controller.  That controller will then spawn an RNG seed generator that will seed itself from the system clock.

Next, render threads are created for each available core and pointers to the settings, tile_buffer, tile_pool and scene.  Each thread is then launched, creating lighting and sampler objects based on the render settings and a vector array of sample accumulators.

A thread starts out by querying the controller's tile pool for available work.  If a tile is available, it will take that tile outline along with a seed value from the controller RNG.  The rendering process starts.  For each ray hit the HitRecord, ScatterRecord and Ray will be passed to each sample accumulator.  Each accumulator will process the inputs to derive whatever sample they need out of it.

When the tile is finished, a new Tile object is created and each sample accumulator flushes it's sample buffer into a pixel buffer that is then added to the Tile.  After this the final Tile object is pushed to the tile_buffer.

After all this, the front end is left with a vector array of tiles, each tile holding the pixels for all the views (AOVs) in the image.  If those images need to be processed or exported, the front end will deal with calling the appropriate exporter or converter class and passing the tile buffer into it.

## The challenges for me regarding this design:

The biggest challenge by far has been the brainstorming behind the render process itself.  There are so many things that need to happen during this process, and I'm really trying to take my earlier post about planning to heart.  That means I need to have this all sketched out before I start to code.  Figuring out how the render threads would pass info to their accumulators, and how those accumulators then transfer their data to the Tile object was really challenging. 

 I'm taking **"1000 yard stare at some random person for a good five minutes because your brain isn't processing what your eyes are doing"** challenging.  

I'm sure this is all old hat to an experienced render engine developer, and I could easily copy what appleseed or PBRT do if I wanted.  What keeps me from doing that is something that's becoming more and more important to me with regards to RIFT: I have to figure the design out myself.  Just copying other programs doesn't teach me squat.  True, RIFT will most likely run like absolute steaming crap compared to better engineered renderers, but that's okay.  As I learn I can always re-write it.

<br>





![](/images/city-journey-orange-and-blue-wall-art_pe1043.jpg)

<br>

Is it too late to say **"Cheesy Inspirational Quote Warning?"**
