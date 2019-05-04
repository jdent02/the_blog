---
title: "appleseed"
description: "appleseed Project Work"
author:
  name: "Jon Dent"
  desc: " "
date: 2019-05-02
draft: false
cover: "images/appleseed-github-organization-logo.png"
categories:
- projects
tags:
- appleseedhq
- opensourcerendering
---

**https://appleseedhq.net/**

appleseed (yes, the lowercase 'a' is intentional, much to the annoyance of every spell checker in existence) is a physically based path tracer that was started as a hobby project by Franz, a former Mental Ray engineer.  Over the years it's been active it has grown into a very capable path tracer.  Sure, it's a bit feature incomplete, but it does a lot of things really well.  

I started working with the appleseed team in November or December of 2017.  At first I worked exclusively on the [Blender plugin](https://github.com/appleseedhq/blenderseed), which at the time had just risen from the dead and become active again.  In 2018 I started my first major project: creating a system for dynamically generating shading nodes in Blender based off the metadata in appleseed's OSL files.

After that success, I began working with Esteban (another major contributor to appleseed) to re-engineer the entire Blender plugin so it would utilize appleseed's Python bindings.  Prior to this, the exporter would write out .appleseed files, feed them into the command line frontend for appleseed, and then pipe the results back into Blender using a custom tile callback that put the results of appleseed's image buffer into stdout, where it was then read by Blender.  Nifty, but not ideal.  With the Python bindings, everything would stay internal to Blender.

Oh, and somewhere in this time frame I ended up as the lead developer of the Blender plugin.  Mostly by default, but still.  I also decided that I wanted to learn C++ so I could contribute features to appleseed that I needed for the Blender plugin.

The next major project (that is still ongoing) is another major re-write, this time in order to get the plugin to work with the upcoming Blender 2.8.  Expect some blog posts on this one...

In the meantime, here's a little video that shows off appleseed nicely.  It's a bit old by this point, but it's still quite charming.



<iframe title="vimeo-player" src="https://player.vimeo.com/video/92172277" width="640" height="360" frameborder="0" allowfullscreen></iframe>