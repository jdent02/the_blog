---
layout: blog
title: "Python to C++: Or How I Learned to Stop Worrying and *Sort of* Love the Curly Brace"
date: 2019-05-03T17:40:30.148Z

description: ""
author:
  name: "Jon Dent"
  desc: " "
draft: false
categories:
- musings on coding
tags:
- C++ 
- Python
cover: images/fear_change_cut.jpg
---
Garth said it best:

![](/images/fear_change.jpg)

I was fairly comfortable with Python, the only language I'd used so far.  It's pretty straightforward, and I knew it well enough to get the things out of Blender that I needed.  Then came a time when I noticed that there were some things missing in appleseed's Python bindings that I needed.  Problem was that appleseed is written in...

C++

For guys at my level, C++ is a behemoth.  So many terms thrown around: pointers, STL, exception handling, polymorphism, subsets, metaprogramming, public vs private vs protected, you get the idea.  For poops and giggles I'd taken a look at appleseed's source code multiple times, and each time this was my reaction:

![](/images/run_away.jpg)

I could barely comprehend what I was seeing, let alone even try to understand the flow:

```cpp
void Intersector::fixed_offset(
    const Vector3d&                 p,
    Vector3d                        n,
    Vector3d&                       front,
    Vector3d&                       back)
{
    //
    // Reference:
    //
    //   Quasi-Monte Carlo light transport simulation by efficient ray tracing
    //   http://vts.uni-ulm.de/docs/2008/6265/vts_6265_8393.pdf
    //

    // Offset parameters.
    const double Threshold = 1.0e-25;
    const int EpsMag = 8;
    static const int EpsLut[2] = { EpsMag, -EpsMag };

    // Check which components of p are close to the origin.
    const bool is_small[3] =
    {
        abs(p[0]) < Threshold,
        abs(p[1]) < Threshold,
        abs(p[2]) < Threshold
    };

    // If any of the components of p is close to the origin, we need to normalize n.
    if (is_small[0] | is_small[1] | is_small[2])
        n = normalize(n);

    // Compute the offset points.
    for (size_t i = 0; i < 3; ++i)
    {
        if (is_small[i])
```

WTF does any of that mean?  What the hell is '&'?  Why is 'void' at the beginning? What is 'const' or 'static'?  So many unknowns...  why can't I just do

```
x = y
```

Like I can in Python?  To top the confusion off, the term "Quasi-Monte Carlo" was familiar, but had no idea what it really was (more on that in a separate post).

So for a while I mostly harped on Franz and Esteban (mostly the latter) to get them to add the features I needed.  Eventually the reality set in that these were busy guys, and my requests simply weren't a priority.  Eventually, I took it upon myself to add the stuff I needed.  At first, all I needed to do was copy existing code verbatim and replace a few names, so it goes without saying that those worked just fine without any real problems (and this is why you have volumetrics and post processing stacks in blenderseed).

Then I came to the same epiphany that I had back when I was involved with PRMan for Blender, the same epiphany that led me to learn Python: if you want to get something done, you'd better try to learn how it works so you can do it yourself.  

So I went looking for a way to learn C++ without going to college, again.

**Enter Udemy.**

Udemy is like a large shopping mall.  Think Mall of America, only without a food court or Camp Snoopy (don't care what it's called these days, it will always be Camp Snoopy to me).  You can find courses on pretty much anything in there.  Unfortunately, if Udemy is a mall, then that mall is in the middle of the Wild West and the sheriff is out to lunch, or at least busy visiting the toilet to fire out that breakfast burrito.  You have to be careful you don't spend your money on a course that ultimately teaches you nothing (i.e. Underwater Basket Weaving for Absolute Beginners).  Luckily I made a few good choices, and 40+ hours of video later, I felt confident enough to look at appleseed's source code again:

```cpp
void Intersector::fixed_offset(
    const Vector3d&                 p,
    Vector3d                        n,
    Vector3d&                       front,
    Vector3d&                       back)
{
```

Okay, that's telling me this function doesn't return anything, it takes in four Vector3d variables, one of them is being  copied, and the other three (with the dreaded '&') are references (i.e not copies).

Wow, cool.  I understood a good chunk of the code now.  Didn't mean I understood the goal this function was accomplishing, but at least I could read the road map.

With these courses under my belt, I started doing more with C++ inside appleseed.  After a while I actually started to like C++.  Don't get me wrong, it's not perfect, and when things break they REALLY break.  I damn near cried when I tried to decipher my first template compilation error, and I probably did cry at my first "undefined external symbol' linker error.

But I always had the appleseed guys to turn to.  It wasn't their job (and I'm sure I caused more than a little annoyance) but they were always willing to help me understand this beast of a language.

BTW if anybody's curious, the main courses I took were Learn Programming Academy's C++ course and Cave of Programming's C++ course (which is technically in two parts).  Fair warning, Cave of Programming is run by a man named John Purcell, and while his content is great, his voice is insanely smooth and very British.  Couple that with soft piano intros and a recording quality that is uncomfortably intimate (it sounds like he's talking to you from inside your head), and you get wonderfully informative videos that run the risk of putting you to sleep.  He sounds just like the guy on the guided meditation YouTube channel my wife watches.

<br>

<h2>So do I recommend C++ for someone beginning to code?</h2>

Umm, **hell no**.  C++ has a well earned reputation for being complex.  The core syntax itself isn't hard to understand or use, but so much of modern C++ is based around using the STL (Standard Template Library) and it is not anywhere near as simple.  Just an example:

Raw pointers in C++ (a core feature) are easy to create, can be easily passed in and out of functions, constructors, destructors, etc...  Conceptually they aren't difficult to understand either.  But they are a perfect example of Uncle Ben's saying that "with great power comes great responsibility"(sic).  In order to make life 'easier', The STL has something called a smart pointer.  They allow the programmer to relax a bit, and they're less likely to destroy the universe.  However, moving one around in and out of functions isn't always simple.  You have to use .get(), std::move() and a few other commands to use them properly (to say nothing about trying to use them in a .dll library).  It's a trade off that isn't always worth it, IMHO.

If you're just getting started in coding, pick something like Python.  It's syntax is kinda similar to C++ (and pretty much every other 'standardized' language for that matter), with a lot of the danger removed.  No, your resulting program won't be anywhere near as fast as something written in C or C++, but if you're just starting out chances are you aren't coding a database system, so speed doesn't really matter.  If you're still hungry, then by all means jump into C++.  It's a fascinating language, and it powers every major rendering engine out there.  It's definitely worth it.
