---
title: ""
date: 2019-05-05T20:54:32-05:00
categories:
- RIFT
tags:
keywords:
- tech
comments:       false
showMeta:       false
showActions:    false
coverImage: "/images/facebook_cover_photo_2.png"
clearReading: true
coverSize: partial
---
[GitHub Repository](https://github.com/jdent02/RIFT_Next)
<h1>What is it?</h1>
The RIFT Renderer is a fairly simple path tracer written in C++14.  It is meant to be modular in design so that every subsystem can be swapped out as new ideas and features are implemented.

<h2>Planned Features:</h2>

Lighting Integrators:

1. Direct lighting integrator
2. Simple path tracing integrator
3. Material importance sampling integrator
4. Multiple importance sampling integrator

Materials:

1. Diffuse
2. Metal
3. Glass
4. Plastic
5. Uber Shader

Objects:

1. Triangle Mesh
2. Sphere Object
3. Infinite Plane Object
4. Rectangle Object
5. Cube Object

Cameras:

1. Thin Lens Camera (with depth of field)
2. Pinhole Camera
3. Fisheye Lens Camera

Random Features:

1. Several Random Sample Generators (QMC based and purely random)
2. Transformation Motion Blur
3. Embree Raytracing Core
4. OpenImageIO based texture input/output
5. Random Noise Generators

<h2>Design Documents:</h2>
Coming Soon...