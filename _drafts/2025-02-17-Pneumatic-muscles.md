---
layout: post
title:  "Pneumatic muscles"
date:   2025-02-17 10:58:35 -0700
categories: Projects
comments: true
---

## Pneumatic muscles
Before getting into this, let's add context. There have been a couple of projects where I need to have actuators at some distance from where the object will anchor. One is a bipedal robot - weight on the legs can help stabilise and reduce inverted pendulum effect, but it also means it adds a torque to the limbs (this is likely negligible. Typical biped legs do not swing out to high angles). Another one involves a large glove that goes over my hand and extends far out, and I want the fingers to actuate. Using electric motors of sufficient strength here means having a number of motors likely fixed a bit out from my hand, when this device is anchored on my arm. That's a considerable torque, and an uncomfortable one too (unless it's shoulder day).

### Why not electric motors
There are several reasons, but first and foremost lets say this: electric motors can and will work here, and are probably better in just about every way. They have fine-grained angular and speed control, they're strong, easily obtainable, easy to run, bidirectional, no hassle with pressure and leaks, and batteries have higher capacity. Having said that, pneumatic muscles offer some of their own advantages - they're light (a big factor for a wearable costume), they have a better strength to weight ratio, they can bend around joints, and are robust to deformations. 

The biggest reasons i want to try pneumatic muscles is the strength to weight ratio - specifically, the weight part being low - and also ... it's something new to try! Yes, conventional robotics rarely if ever uses these, but hey, it'll be a good exercise and make for an interesting project at the very least. 

### What is a pneumatic muscle?
These are sometimes called McKibbens muscles. There is a flexible hollow part, the actuator, that will be pressurised. This usually is split into a bladder and some sort of expanding mesh (think a Chinese finger trap) to take the strain. At each end is a rigid cap - these form attachment points to the bodies to be acted upon. Feeding in through one rigid cap is a supply line of air. When the air pressurises the bladder, it expands and so does the mesh - radially. Therefore, it axially - lengthwise - contracts, which pulls the ends together with some force proportional to the pressure. Typically, one end is attached to a frame, and the other to a part that needs moving, or it is attached to an arm around a rotating joint, and this pulls the arm in a rotation. 

[](/assets/pneumatics/diagram.png)

Think of it like a regular muscle - your bicep is attached to the humerus (upper arm bone) and to the radius/ulna (forearm bones) - and it attaches across a rotating joint (the elbow). When you lift using your bicep, it is contracting, which pulls the two bones together. The forearm rotates towards the upper arm about the elbow with some force. 

### Construction
I've tried to make these relatively cheaply. The actual muscles themselves are from lengths of plastic mesh/weave (chinese finger trap? No idea what to search) TODO add link, and 700cc bicycle inner tubes. The end caps use brass pipe compression fittings from Home Depot - <list exact parts here>, and these go to a 5mm hose that can plug into 1/4" press-fit couplings and a 12v solenoid

Add pictures here to describe everything

### Testing
Ideally a costume or movable robot would have its own compressed air supply - and a compressor, if not multiple carbon dioxide cannisters. Given that this is just in the testing/early development stages, I'm attaching it to my air compressor with a 150psi tank at however many gallons - basically, a lot of air, but a lot of air that is not particularly mobile. I have been running the system at 50psi, but can crank it to 75. 

Now for some measurements. How strong is each muscle? How far does it contract?

stats go here

