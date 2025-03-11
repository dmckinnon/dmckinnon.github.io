---
layout: post
title:  "Pneumatic muscles"
date:   2025-02-17 10:58:35 -0700
categories: Projects
comments: true
---

## Pneumatic muscles

[](/assets/pneumatics/muscle.jpg)

Before getting into this, let's add context. There have been a couple of projects where I need to have actuators at some distance from where the object will anchor. One is a bipedal robot - weight on the legs can help stabilise and reduce inverted pendulum effect, but it also means it adds a torque to the limbs (this is likely negligible. Typical biped legs do not swing out to high angles). Another one involves a large glove that goes over my hand and extends far out, and I want the fingers to actuate. Using electric motors of sufficient strength here means having a number of motors likely fixed a bit out from my hand, when this device is anchored on my arm. That's a considerable torque, and an uncomfortable one too (unless it's shoulder day).

### Why not electric motors
There are several reasons, but first and foremost let's say this: electric motors can and will work here, and are probably better in just about every way. They have fine-grained angular and speed control, they're strong, easily obtainable, easy to run, bidirectional, no hassle with pressure and leaks, and batteries have higher capacity. Having said that, pneumatic muscles offer some of their own advantages - they're light (a big factor for a wearable costume), they have a better strength to weight ratio, they can bend around joints, and are robust to deformations. 

The biggest reasons i want to try pneumatic muscles is the strength to weight ratio - specifically, the weight part being low - and also ... it's something new to try! Yes, conventional robotics rarely if ever uses these, but hey, it'll be a good exercise and make for an interesting project at the very least. 

### What is a pneumatic muscle?
These are sometimes called McKibbens muscles. There is a flexible hollow part, the actuator, that will be pressurised. This usually is split into a bladder and some sort of expanding mesh (think a Chinese finger trap) to take the strain. At each end is a rigid cap - these form attachment points to the bodies to be acted upon. Feeding in through one rigid cap is a supply line of air. When the air pressurises the bladder, it expands and so does the mesh - radially. Therefore, it axially - lengthwise - contracts, which pulls the ends together with some force proportional to the pressure. Typically, one end is attached to a frame, and the other to a part that needs moving, or it is attached to an arm around a rotating joint, and this pulls the arm in a rotation. 

[](/assets/pneumatics/diagram.jpg)

Think of it like a regular muscle - your bicep is attached to the humerus (upper arm bone) and to the radius/ulna (forearm bones) - and it attaches across a rotating joint (the elbow). When you lift using your bicep, it is contracting, which pulls the two bones together. The forearm rotates towards the upper arm about the elbow with some force. 

### Construction
I've tried to make these relatively cheaply. The actual muscles themselves are from lengths of plastic mesh/weave (chinese finger trap? No idea what to search) TODO add link, and 700cc bicycle inner tubes. The end caps use brass pipe compression fittings from Home Depot - see the list below - and these go to a 5mm hose that can plug into 1/4" press-fit couplings and a 12v solenoid.

List of end cap parts:
- 1/2" OD to 1/2" ID compression (x2)
- 1/2" ID cap
- 000 cork (cut in half, insert into sleeve beneath cap)
- 1/2" sleeve (x2, should come with coupling)
- 1/2" FEM OD to 3/8 OD adapter (LFA 230)
- 3/8" FEM to 1/4" OD adapter, LFA 157
- 1/4" sleeve
- 1/4" cap with a hole

![](/assets/pneumatics/parts.png)

Feed the bike tyre and mesh through the compression coupling, and insert the sleeve. Push this down into the coupling for a tight fit:

![](/assets/pneumatics/tyre.png)
![](/assets/pneumatics/coupling.png)

On one end, add the cap. If this does not hold pressure, cut the cork in half, jam the cork in the sleeve, then add the cap back. The cap should press the cork into the sleeve, and maintain pressure.
On the other end, add the adapters to convert down to 1/4". The plastic pipe I use to route between solenoids, the 5mm hose, fits tightly onto this. Push a sleeve through the 1/4" adapter, and jam the 5mm tube onto this. Add the cap and plumber's tape for a tight fit

![](/assets/pneumatics/cork.png)
![](/assets/pneumatics/small_end.png)

Now feed the other end of the tube into a press-fit coupling, this goes into the solenoid ... and bam. Air. 

![](/assets/pneumatics/whole_thing.jpg)

### Testing
Ideally a costume or movable robot would have its own compressed air supply - and a compressor, if not multiple carbon dioxide cannisters. Given that this is just in the testing/early development stages, I'm attaching it to my air compressor with a 150psi tank at however many gallons - basically, a lot of air, but a lot of air that is not particularly mobile. I have been running the system at 50psi, but can crank it to 75. 

Now for some measurements. How strong is each muscle? How far does it contract?

Contraction:
- 25 psi
- 50 psi
- 75 psi

Lifting weight:
This was harder to do rigorously as I didn't want to do it to breaking point
- 25 psi:
- 50 psi:
- 75 psi:

Weight of 1 muscle (length Xmm): 114g

## Different components
The couplings I've used in this muscle have been brass. One muscle is ~110g. For comparison, one stepper motor is maybe 400g, so we're already saving considerable weight if you think about holding this in a costume on your arm for an hour, or on the end of a robotic limb. But can we get this lighter? Also, save costs? Each brass coupling part is maybe $5-10, and with five-ish partspre muscle, that's going to add up. 
What could we use?

### Resin
I researched engineering resins for resin 3D-printing - Siraya Blu Tough, Phrozen Rigid series (Onyx Impact Plus, for example) - and got Siraya Blu tough (and what to try Phrozen Onyx 410). 

People have printed threaded parts from these resins - [this man](https://www.youtube.com/watch?v=7r16-UDo2t4) printed bolts and nuts in Siraya Blu Tough and tested them for shear strength, tensile strength, practicality (the threads needed to be tapped again), etc. 

Can we print from these parts and get an airtight seal? Will the muscle be just as strong? (well, no - brass will still be better. Will it be strong _enough_?)

#### Siraya Blu Tough
I printed parts from Siraya Blu
photo

My printer is definitely capable of high precision - I've seen this on printing minis for D&D. But these threads ... they are definitely not defined enough. Is resin getting stuck in the threads and not getting cleaned out? The inner thread worked and connected to the metal, and meshed well. 

I tried several different tolerance levels and cleaning strategies:

#### Phrozen Rigid Onyx 410


