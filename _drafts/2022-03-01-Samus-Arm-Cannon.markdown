---
layout: post
title:  "Samus' Arm Cannon"
date:   2022-03-01 10:58:35 -0700
categories: Projects
comments: true
---
photo with cannon circled



This all started from a silly lunch conversation at work where we ended up brainstorming what one could reasonably fit into an arm-cannon-sized prop that would be useful outside fighting aliens - I'd suggested a fold out broom and vacuum (roombas can be quite compact; why not this?), someone else said a windex spray cannister, someone said a flamethrower ... and then I found a really well made [complete model online]() and thought ... yeah, I could actually make this. I decided to start with adding a Nerf gun as the main feature before anything else fancy, and then later decided not to add anything else or do other versions as these were really hard to make. The vacuum cleaner-combined-broom-combined-windex would be fun though.

## Contents
- [Materials](#materials)  

- [Modelling and printing](#modelling-and-printing)

- [Electronics](#electronics)

- [Paint](#paint)

- [Final product](#final-product)

- [Models](#models)

### Materials
Given that I'd found the model on [Thingiverse]() I was always going to 3D print the main structure of the cannon, and I typically use PLA; the question remained for what to do about all the other parts. I had originally thought to use a [Raspberry Pi]() for the brains; this would let me have a nice touch screen interface in the cannon for controlling things like nerf gun power, lights, reading battery levels, etc - this turned out to not work (read on in [Modelling and printing](#modelling-and-printing) and [Electronics](#electronics) sections). 

I bought a make-model nerf gun to use inside, and there was one reason with two main advantages behind it for this choice: it was a flywheel ball-based nerf gun. Normally nerf guns fire small foam darts, using either a spring or a blast of compressed air. A spring mechanism would have required the spring to sit behind the point of firing, adding an extra six inches or so onto the length already needed. Compressed air could have been piped from further down my arm, but would have needed either cannisters or some mechanical motion to recompress the air, which I expected to be difficult. Then there's the darts - normally these are stored vertically in a clip:

photo

and this would not fit easily into the arm cannon. I could stack them end-to-end, but they'd then need to be shifted sideways at some point. 

The Nerf Gun used these little foam balls

photo

which meant that I could store them in a pipe running down the length of the cannon next to my arm, and that they could then roll into the firing point easily - no need to stack one direction, and then move in a different direction. Tis would be much easier to design. 

Secondly, the flywheel:

photo

This propels the balls by having the trigger activate the flywheels, spinning them up to speed, and then pushing the ready ball far enough into the wheels that they catch it and fling it out. As this resets the next ball can roll forward up until a point, where it would need to be pushed to go further. This is much more compact than a spring, and all I would need to do is mount it in front of my fist in the cannon and allow the balls to roll into it (or so was my thinking). 

The last material consideration was lights - I wanted LEDs in this, as the arm cannon glows in the games

photos

and has particular effects when charging, firing, etc. From the games I could see that it's typically an orange glow, pulsing along those inset channels. The model I used had these, so I thought to mount LEDs on the inside of the cannon, and the sparse structure of the 3D print should allow enough light through whilst also dispersing it from being points to being a general smudge. [Neopixels]() was the easiest route - these are technically just WS2812 LEDs on a flexible copper backing, but with considerable software support for raspberry pi and arduino. 

All the pieces seemed in place to me, so time to start modelling.

### Modelling and printing
I started to model this in [Tinkercad](), which, while it's a great program, was an outright mistake. It does not give me the fine-grained control I need for a model this complicated. So I soon moved to Fusion360, an absolute beast of a program that I still barely understand, but could do considerably more in. This allowed me to easily slice the model into its different sections: the front piece, the cone, three sections of tube, and the elbow piece:

photo

This would make both printing and prototyping easier - smaller prints, quicker turnaround, and less wasted pieces. As I modelled other parts I added screw-based joins between these pieces so they could easily come apart if needed. 

First thing to sort out was sizing - the arm cannong comes up to Samus' right elbow, so this needs to fit my entire right forearm and hand, and then also have room for the nerf gun at the front. I measured my arm length with my hand in a fist (I had always imagined that there would be a crossbar I would hold to keep the cannon on, and have controls on), the length of the nerf gun flywheel piece, added a couple of ball-diameters to account for room for the barrel coming in and a firing mechanism, and then decided that this should all fit in the main tube - the cone and smaller tube at the front would just be for lighting and the balls flying through on their way out:

photo of pieces

This was also the easiest piece to stretch without stretching any existing detail beyond recognition. Next in sizing was diameter - again, this has to fit my forearm and the nerf gun, so it needs to be wide enough. I went to Home Depot's PVC pipe section and stuck my arm in different PVC pipes to see how tight various diameters fit, how much motion I had, how much a closed fist rubbed against the sides, and so on. 4" was an almost perfect fit, so I scaled the model up to that and started some test prints.

#### The nerf gun
The hardest thing to fit was always going to be the nerf gun flywheel, and I wasn't sure how best to do this, so I printed a test cone piece and sat around measuring and modelling:

photos of real and model

Perhaps I should have modelled the actual flywheel, but at this stage that was possibly beyond my skill? I worried I wouldn't do it accurately, and that this would propagate more errors through the whole build. It ended up being a really tight fit - I could not have made the cannon any narrower - but with some grinding down of plastic edges I fit the flywheel jutting up into the cone, which would leave a decent amount of room for a firing mechanism between this and my hand. To hold it in place I created supports to match the existing flywheel supports and hold them, as well as a back support piece to hold it from the other side. Here's the first iteration, and then I realised I'd messed up the screw join panels to the front piece, so I did a few other updates while I was there and made a final design:

photo

This supported the flywheel nicely, so I then moved to the first section of tube to add the rest of the flywheel supports, a screw join to the cone, and the beginnings of a barrel to lead into a firing mechanism. There was some consideration into the relative rotation of the flywheel to the cannon tube, since it had the inset channels and I wanted to stick LEDs on the inside to shine through, so I had to make sure that the flywheel supports did not get in the way of where I wanted LEDs. This worked well, until I actually tried to fit the LEDs in there and realised they sat _just_ below the actual wheels - and by just I mean the wheels scraped them until I got in there with a file and filed the wheel edges down. Could have decided this better, or alternatively just modelled a trench for the LEDs to sit in opposite the inset channels. It was getting very thin already, and ... I thought of this idea too late. Ah well. I'll further discuss the final LED mounting later. Back to the cannon. 

Once I was working on adding the barrel for the nerf balls in and the supports for the flywheel onto the first part of the tube, I had to decide the relative rotation of the cone to the main tube. The reason for this is my arm had to fit down there, and my elbow locked the elbow piece into place, so it couldn't rotate relative to my arm. The tube connecting to this had exterior designs that carried across both pieces, so it had a locked rotation too. The rest of the tube did not, but I did want a channel aligned with the screen:

photo

And given the shape of my arm, the barrel had to fit down the underside of my wrist, and curve up towards my hand, or on top of the wrist and curve over the fist down into the flywheel. I eventually settled on under the wrist, and modelled a cylinder a bit bigger than a nerf ball that ran down the tube and curved into the feeder point on the flywheel:

photo of partial piece

I reasoned that any crossbar for my hand to hold and press buttons on could a) be added later, and b) would only get in the way for now, so it was excluded. Then came the question of how to push the balls from the feeder point into the flywheel, and at the same time prevent them from _accidentally_ going in there. A servo sounded like a good idea, one of those super light micro servos (link) that can practically be powered from an arduino. I needed a few things:
+ a mount for the servo on the flywheel piece
+ a rotating servo arm that would stop a ball going through with gravity, but push a ball through when wanted
+ this arm also needed to not let the next ball through until after it had pushed the first and reset
+ after some experiments, this also needed a support for the arm so it couldn't come off the servo. 

Let's go over my design. Here's all the components:

photo

The support had to be designed in conjunction with the arm, as the arm length affected where the servo would sit. I sliced a slit with a dremel and hacksaw down the middle of the flywheel piece to accomodate a rotating arm from the servo, and made it thicker and longer than necessary - the ball would still easily be supported. The main part of the support was a brace around the servo, and what remained was any extension required to precisely place it, so I came back to this after designing the arm. For the arm, I started with a basic L shape:

photo

The little arm of the L would sit between the ball and flywheels in the default position, and prevent accidental firing. The ball itself would not be able to move to the sides from the given flywheel barrel shape, and the long arm of the L would stop it from moving backwards. By having the barrel end a hair more than a ball diameter away from the feeder point, this prevented more than one ball at a time in the feeder. So far so good. Then for firing, the servo would rotate this forward - the little arm drops out of the way into the slot I cut, and the long arm of the L comes forward to push the ball into the flywheels. Two problems:
+ what's stopping the ball from being pushed up out of the flywheel track completely?
+ what's stopping the next ball from falling into the feeder and back towards my arm while the current ball is being pushed into the flywheel?

I redesigned the arm and the barrel to fix both these problems: the barrel got a little extended roof that would stretch over toward the flywheels, to keep any ball in the track, and the arm got a curved back:

photo

This would hold any balls in the barrel at bay as it curved forward, only letting one drop when fully back. I created a servo mount that accomodated this, gave it a test, and it worked! A bit. The end part needed to be thicker, otherwise it would attempt to push the ball and just bend to one side due to the plastic being weak. The other thing that happened was the arm would occasionally come off the servo. 


#### Circuit board mounts and access

#### Fitting my arm

#### Extra detail

### Electronics

#### How many arduinos?

#### 

### Paint
Given that this was 3D printed, I had to sand the surface smooth before painting, otherwise you get these print artefacts where you can see the layer lines. First a 60 grit pass, then 120 grit, then 240 grit to polish it off. This was particularly difficult around the little edges, nooks, and crannies, but I got most of it:

photo

Then an undercoat layer. I was going off [Frankly Built's]() painting guide to start - automotive primer, then a base colour layer, then automotive metallic finish. It was difficult finding the right undercoat colour for the metallic green I was using, since these automotive metallic finishes let some undercoat colour through, but not much - eg. for other builds I did gold underneath with a red finish, that mostly looks red but at certain angles you can see the gold combine in with a really nice effect. The primer at least covered most of the sanding imperfections

photo

and after that I did an under-undercoat of yellow to get the colour of the inset channels baked in early - then I covered these in masking tape. It took a few cans to get a yellow that looked right:

photo

Finally, I experimented with a green underlayer, a yellow underlayer, before settling with a base gray - bland, but the final colour actually looked like what I was aiming for:

photo

The yellow channels did need some fixing here and there since my masking tape was not perfect. I did this with some yellow paint and a thin paint brush



### Final product
And here's how it turned out!



### Models
There's really only one model available online that I used for this, and I've already linked it multiple times but here it is again, explicitly: 

Big thanks to [] for modelling it and making it available for free.

