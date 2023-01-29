---
layout: post
title:  "Reaction Wheel Experiments"
date:   2023-01-31 10:58:35 -0700
categories: Projects
comments: true
---

After watching some reaction-wheel control videos on youtube (tom stanton, james bruton), I wanted to make my own reaction wheel project. But do I use a reaction wheel for balance control? No! I'm going to - for better or worse - use it for _drive_. This could be completely silly, but I think it's a fun idea and a good learning experience, so here we go!

There are many introductions to and explanations of reaction wheels online; (links) a simple primer is that these wheels add an angular momentum component to a body, typically to react against a force like gravity, or a push. A reaction wheel on the end of a rotating arm can resist that arm's rotation by adding a counteracting angular momentum. 

My logic then is if you have a body that can roll, a reaction wheel can give the entire body angular momentum enough to roll forwards or backwards. 

## Contents
- [The Base](#the-base) 

- [V1: Brushless Motors](#v1:-brushless-motors)


### The Base
Until I come up with a better design, here is the base shape I'm working with:

photo

two ~30cm wheels on the sides, rigidly attached to a battery bay in the middle that also has circuit boards attached to it: an ESP32 dev kit, a power distribution board, and whatever motor driver boards are necessary (L298N, brushless ESC, whatever). The battery is a 3S lipo for ~12v, and as said the brains are an ESP32 for wifi. I've also written a desktop program that can send control packets via UDP to drive it remotely. 

### V1: Brushless motors
For the first iteration, I had some brushless DC motors sitting around from an old drone. These have plenty of speed, so worth a shot. I printed some small reaction wheels and added weights (bolts) to the outside:

photo

The ESP would hold at a low throttle signal until the drone ESCs armed, and then slowly raise throttle to a small limit. This proved pretty successful ... once I got past the arming finickiness. However, it was not without downsides - the robot just rocketed forwards
