---
title: RIFT Render Project
date: 2019-05-07T15:33:15.045Z
summary: A Reduction in Free Time
keywords:
  - RIFT
categories:
  - Rift
tags:
  - rift_renderer
thumbnailImage: /images/sphere-s.jpg
thumbnailImagePosition: right
coverImage: /images/cornell_box.jpg
coversize: partial
showDate: true
showTags: true
showPagination: true
showSocial: true
showActions: true
---
# The RIFT Render project

Those of you that have clicked on the little GitHub logo on the left likely noticed a repository for something called RIFT (after marveling at my awesome avatar of course).  What is it?  The caption says "My sandbox renderer. Inspired mostly (but not entirely) by appleseed, Peter Shirley's Raytracing series, and PBRT".  Okay, you think, so this guys writing a renderer too.  Why?  Aren't there enough pet path tracers out there to choke the Pacific?  

Yep.

There are a lot of open source path tracer projects out there.  Some of them are serious tools (like [Mitsuba](https://www.mitsuba-renderer.org/), [appleseed](https://www.appleseedhq.net), or [LuxCore](https://luxcorerender.org/)).  Others are personal projects, and their only reason for existing is to provide a hands on learning tool.  RIFT most definitely falls into the latter.  I have no intention for it to be (or delusions that it might be) used in any kind of serious capacity.  Compared to the above mentioned renderers (and serious production level renderers like PRMan or Arnold), RIFT is going to run like a slug, and it'll likely never support many of the features those render engines take for granted.

With that being said, here's how it started.

## The Roots of RIFT

RIFT started as a straightforward implementation of the simple path tracer described by Peter Shirley in his now famous trio of eBooks: [Raytracing in One Weekend](https://github.com/petershirley/raytracinginoneweekend), [Raytracing: The Next Week](https://github.com/petershirley/raytracingthenextweek), and [Raytracing: The Rest of Your Life](https://github.com/petershirley/raytracingtherestofyourlife).  At first I dutifully transcribed the code from his examples verbatim, and in a short amount of time had a working image:

![](/images/sphere-s.jpg "Oooh, balls")

Pretty cool.  There's a raft of different techniques and features in that one simple shot: multiple material types, random location and color generation, transformation motion blur, a rudimentary BVH, and an emissive background.

It didn't take long for some of the shortcomings of this implementation to come to light though.  

For one, everything was hard coded.  The generation of the shapes, the materials, even the lighting was non-adjustable unless you changed the C++ code.

Next up was some really sloppy use of pointers.  Most everything in the scene is stored on the heap and referenced by a pointer.  Normally not a big deal (and fairly standard practice from what I've seen).  The problem was nothing was getting cleaned up at the end of the render.  I know, I know.  Technically it doesn't matter because when the program terminates, all the allocated memory is made available again, but that missing step really slammed up against my coding instincts.  

Next off, the options for image output were fairly limited (to say nothing of interactively displaying an image).  Plus, gamma correction and sRGB conversion was happening inside the actual path tracing loop, and the results were being stored as integers (making high dynamic range output impossible).

Then, I noticed that the examples used a lot of division operations, which are notoriously slow even on modern CPU's.  

Next, it only supported spheres and rectangle objects (that ground plane is actually a very large sphere).  

Finally, it didn't even have multithreading (pretty much a requirement these days).

All of these missing features (and other design quirks) led me to start modifying the code even as I was going through the books.  

Firstly, the scene creation process was moved to a configurable generator (allowing for several 'pre-made' scenes).  

Next, many of the division operations were replaced with inverse multiplications instead (I got the trick from PBRT but they certainly didn't come up with it).  

The raw pixel buffer was switched to floats, and the gamma conversion and sRGB compression now happened in a separate "output" module.  That same output module also gained OIIO support.  Finally, I added a primitive but effective multithreading module.

All these changes were under the hood though. I didn't add any new features to the renderer itself.

I continued to plug away at the books, and by the time I was done I had this:

![](/images/cornell_box.jpg "Ooh, a box")

At this stage, the renderer had material importance sampling, a decent material system (including texture support), and even homogenous volumes.  Not a bad start, but still quite far from something truly useful, and this was the point where RIFT was born.  Originally, my plan was to update certain sections and start adding new functionality to the renderer.  

It didn't take long for this plan to change.

The more and more I dug into the guts of how this renderer worked, the more and more I wanted to change.  So I decided I was going to re-write the renderer from scratch.  I'd take Shirley's concepts and integrate them into a renderer that was truly modular, with a coding style that would attempt to be as 'legitimate' as possible.  This meant proper initialization and cleanup of objects, and an emphasis on memory and computational efficiency.  Aaand, it would follow appleseed's example by being a shared library that was then accessed by stand alone programs.  

Big plans for a novice developer who was still an FNG when it came to C++.

But I felt confident that I could at least attempt it.  After all, what's the worst that can happen?  The world ends up with another abandoned GitHub path tracer?  Meh, I think it can handle it.  After all, it's handled the Kardashians, so far...

## So what are my goals with RIFT?

Ultimately, here's what I see RIFT supporting (eventually, maybe):

1. Shapes:
   1. Multiple object types (sphere, cube, triangle mesh)
   2. Analytic light sources
   3. Emissive geometry
   4. Infinite planes (not realistic but common)
   5. Automatic scene generation
2. Materials:
   1. Diffuse
   2. Glass
   3. Metal
   4. Homogenous volumes
   5. Some kind of Uber shader (Disney or something else)
3. Cameras:
   1. Thin lens
   2. Pinhole
   3. Fisheye
4. Image Processing:
   1. Full OIIO pipeline
5. I/O:
   1. Custom scene description format
6. Features
   1. Tiled rendering
   2. Multithreading
   3. Embree integration (for BVH and ray tracing)
   4. Proper logging

So there it is.  The old RIFT (the version largely based on Shirley and existing as a standalone executable) is still on my GitHub site, but for all intents it's abandoned.  Moving forward, RIFT Next is the place to be.
