---
title: "Projects"
date: 2019-05-05T20:54:32-05:00
categories:
- projects
tags:
keywords:
- tech
comments:       false
showMeta:       false
showActions:    false
coverImage: "/images/abstract-1278077_1920."
clearReading: true
coverSize: partial
---

{{< image classes="fancybox center clear" src="/images/appleseed-logo-512.png" >}}
<h1>appleseed and blenderseed</h1>

**https://appleseedhq.net/**


appleseed (yes, the lowercase 'a' is intentional, much to the annoyance of every spell checker in existence) is a physically based path tracer that was started as a hobby project by Franz, a former Mental Ray engineer.  Over the years it's been active it has grown into a very capable path tracer.  Sure, it's a bit feature incomplete, but it does a lot of things really well.  

I started working with the appleseed team in November or December of 2017.  At first I worked exclusively on the [Blender plugin](https://github.com/appleseedhq/blenderseed), which at the time had just risen from the dead and become active again.  In 2018 I started my first major project: creating a system for dynamically generating shading nodes in Blender based off the metadata in appleseed's OSL files.

After that success, I began working with Esteban (another major contributor to appleseed) to re-engineer the entire Blender plugin so it would utilize appleseed's Python bindings.  Prior to this, the exporter would write out .appleseed files, feed them into the command line frontend for appleseed, and then pipe the results back into Blender using a custom tile callback that put the results of appleseed's image buffer into stdout, where it was then read by Blender.  Nifty, but not ideal.  With the Python bindings, everything would stay internal to Blender.

Oh, and somewhere in this time frame I ended up as the lead developer of the Blender plugin.  Mostly by default, but still.  I also decided that I wanted to learn C++ so I could contribute features to appleseed that I needed for the Blender plugin.

The next major project (that is still ongoing) is another major re-write, this time in order to get the plugin to work with the upcoming Blender 2.8.  Expect some blog posts on this one...

In the meantime, here's a little video that shows off appleseed nicely.  It's a bit old by this point, but it's still quite charming.


<iframe title="vimeo-player" src="https://player.vimeo.com/video/92172277" width="640" height="360" frameborder="0" allowfullscreen></iframe>

<br>
<br>
<br>

<h1>PRMan for Blender</h1>

**https://renderman.pixar.com/**

I got my start in programming by learning Python so I could contribute to what was at the time called RendermanForBlender (now I think it's technically called PRMan for Blender).  I wanted to have the control for anamorphic bokeh exposed, but the main designer at Pixar (Brian Savery) had a full plate.  So I made my very first GitHub commit on April 28, 2016 to add those controls.  After that I ended up implementing much of the export pipeline for AOVs, most of the infrastructure for rendering projects outside of Blender (using Pixars's LocalQueue), opening up the PRMan baking workflow to Blender, and a bunch of other small items.

Unfortunately, in mid 2017 Brian departed Pixar and PRMan for Blender fell into dormancy.  It isn't dead, per se, but Pixar's priorities are definitely somewhere else.