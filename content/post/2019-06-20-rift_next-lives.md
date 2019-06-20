---
title: RIFT_Next Lives!
date: 2019-06-20T16:15:30.401Z
keywords:
  - RIFT_Next
categories:
  - RIFT_Next
tags:
  - RIFT_Next
thumbnailImage: /images/itsalive.jpg
showDate: true
showTags: true
showPagination: true
showSocial: true
showMeta: true
showActions: true
---
After much code typing, some slight headaches, and a lot of reading about the voodoo magic of dynamic link libraries, I'm happy to say that RIFT Next is finally capable of producing an image:

![](/images/cornell_box.jpg)

Well, nothing too impressive on its own, as it's pretty much the same image the older RIFT produced right before it was sent to the retirement community in Renderville, where it frolics with it's protein shake alongside celebrities like MentalRay and REYES Renderman.  It's offspring had to start somewhere, and getting it to the same level as dear old Dad seemed like a good starting goalpost.

Of course any good goalpost needs to be moved...

## So where does RIFT Next go from here?

My immediate plan is....Undetermined.  I'd like for RIFT to have a solid logging and timing system, so I can generate reports on hit counts, time spent in certain areas, error messages, etc...  Surprisingly, designing a logging system is actually kind of hard, especially trying to figure out how to pass messages from the threads to the main logger in a way that doesn't cause huge lock contentions.

There are also some shading errors I need to track down.  The image above doesn't show them too well but there are random black pixels in the image.  I suspect it has something to do with the material importance sampling, as the issue only happens with that lighting integrator.  But it's a randomly occurring issue, so it's gonna be a challenge to find and fix it.

I also want to implement a matrix class for storing positions and rotations.  As it currently stands, the way to get those blocks in the image involves nesting a cube object inside another object that rotates the box, and then nesting that inside a third object that performs the translation.  That's a seriously inefficient system (and a real pain in the butt to keep straight).  It would be nice for the cube object to store a 4x4 matrix that handles all the translation and rotations in one place.  It seems to be the way virtually every other renderer works, so there's plenty of examples out there to follow.

I also want to add in some new objects like an infinite plane and maybe even a cylinder.

Finally, I want to get triangle meshes added in so I can render something more interesting than a Cornell Box or a field of balls (heh heh).  I've had my eye on the Embree library from Intel, so I have to research that.

Finally, I'm wondering how much I may want to change the architecture of RIFT Next based on [Raytracing from the Ground Up](http://www.raytracegroundup.com/), one of the books from that awesome Humble Bundle.  The concepts and overall structure seem similar, but there are major differences in how it's being implemented.  The book is much more advanced and thorough than the Pete Shirley e-books that jump started RIFT.  Whether this means it's render is 'better' is not an easy question to answer, but at the very least it probably has some good tricks and techniques that I can use.
