---
title: The Importance of Design
date: 2019-05-09T13:45:03.662Z
summary: i.e. Why it's a dumb idea for me to code without a plan
keywords:
  - RIFT Renderer
categories:
  - Coding Thoughts
  - Rift
  - blenderseed
tags:
  - pearl_of_wisdom
thumbnailImage: /images/istock_confusion_d0ah3h.jpg
thumbnailImagePosition: left
showDate: true
showTags: true
showPagination: true
showSocial: true
showActions: true
---
One of the things I'd been able to skate by on up until recently was not having to do much planning before I started writing code.  I'd just write as I went along.  Usually it worked out for me.  After all, I wasn't really designing anything new, instead I was expanding existing systems that were already in place.  Even in cases where this wasn't **\*quite\*** true (blenderseed's OSL shading interface was a new design), my preferred method of working still got results because the total size of the new feature was pretty small compared to the overall project.  Hell, I was even winging it during the **"blenderseed re-write of 2018"** (for proper effect, say that with a booming, echoing voice).

Here's where I should say something about the comfort of routine, but that would be <acronym title="If you have to look up what it means, then you're probably doing it">mansplaining</acronym>, and...

![](/images/1_8xraf6eyaxh-mynxoxkqla.jpeg)

So anyhoo.

1. Jon's Old Coding Workflow:
   1. Make it up as you go.  
   2. Backtrack and fix older stuff that doesn't work because of changes.
   3. Make too many updates in a row to older code to account for new features.
   4. Let your shogun approach cause plenty of bugs.
   5. Get confused because you can't remember how you got to where you are, you just know something isn't working.
   6. Fix bugs?
   7. .....
   8. Profit!
   9. Fix more bugs in a random, undisciplined fashion.
   10. Re-write the whole damn thing because you've jacked it up so bad (and didn't document any of it).
   11. S*#$@, now nothing works.
   12. Cry salty tears of bitter defeat.
   13. Go to bed.

Then I started on RIFT Next, and I had my 'Come to Jesus' moment on just how crappy that workflow is.  

For all of it's complexity, blenderseed is essentially a shuttle; it takes data from one system and translates it to another.  It doesn't have to design the back end systems or structures needed for the renderer to work.  I don't need to figure out how the render controller is going to work, or how the rendered data should be handled inside that controller.  

On the other hand, when creating a render engine from scrap you **\*do\*** have to think about those elements.  Especially when one of your goals is to not create a carbon copy of another renderer.  I'm always honest in stating that I take a lot of inspiration from PBRT and appleseed, but I'm still trying to do my own take on it.  That means I have to design, among other things:

1. How the command line should be parsed and what information should be in it
2. How the scene information is generated and stored
3. How the render process is structured:
   1. How is it controlled?
   2. How are tiles created and managed?
   3. How are threads of execution scheduled?
   4. How do those threads run their sampling process?
   5. How do those threads store their results and how do they return those results?
   6. How are the thread results stored?
   7. How does the renderer clean up (what even needs to be cleaned up?)
   8. What needs to be done with the render results to make a viewable image?

There's no way that kind of complexity can be hacked together as I merrily go along.  I really **\*really\*** need to plan all this out.

Of course that's easier said than done.  One of the challenges I continually encounter is that even when I plan, I miss something important.  This is largely due to my inexperience with analyzing code design to that nitpicky of a level.  

Consider the following: 

- I want RIFT to be a tiled renderer (where the render is split up into multiple tiles so each of your CPU cores can work on a chunk of the final image).  Originally I had planned for each worker thread to merge it's results into a C float array 'pixel_buffer' that would be maintained by the render controller.  While that plan would have worked, it would have been sub optimal.  When a tile finished it's process, it would have to lock the buffer while it wrote it's results into it.  If another thread happened to finish at the same time, that thread would have to wait for the first one to finish.  The bigger the image and/or the more threads available, the bigger the traffic jam could get.  In addition, the size of this buffer would need to take into account the format of each pixel.  Was it RGB?  RGBA? That extra letter matters...

- I want to keep each element of the renderer as self contained as possible, so I can easily swap parts of it out.  Given that, is it really the correct choice to have the pixel_buffer stored inside the render controller?  What if I just want to swap the controller out (say by something with SIMD instructions)?

I had already started coding by the time I realized these questions needed answers.  Crap...  More wasted effort, cue another refactor...

So I realized that I need to slow down, and spend some time methodically walking through RIFT and what it needs to do.  I need to think of how all these little systems fit together.  I need to be smarter on how I spend my coding time, because I don't have a lot of it between work and family.

So to make a long story short: old Jon didn't plan well and wasted a lot of time coding stuff that either didn't work or got rewritten once a problem with it came to light.  Don't be like old Jon.  Be like new Jon.  He plans!
