---
layout: post
title:  "A Laser Engraver from an old 3D printer"
date:   2022-09-07 10:58:35 -0700
categories: Projects
comments: true
---

In 2021 I got a new 3D printer - a Creality CR10 V3, a significant upgrade in many ways over my old 200mmx200mmx200mm hobbyking with no display. The old one had finally carked it - the bed no longer heated up, and the control board did not respond to USB. So what was I to do with this old printer? I could hardly resell it, except to an enthusiast for spare parts.

But then I realised - somehow entirely separately to seeing any similar things online, like I'd never engaged with the RepRap community at all - that a laser engraver was simply a 3d printer where the head stayed level. And a CNC machine was a 3D printer that worked by subtraction of material, rather than addition. 

I thought back and forth and read a bunch online and decided upon the laser engraver: they're simpler, have fewer moving parts, and don't need a super rigid frame to handle the spindle vibrations like a CNC machine would. 

## Contents
- [Parts](#parts)  

- [Building a case](#building-a-case)

- [Wiring](#wiring)

- [Firmware](#firmware)

- [Final product](#final-product)

- [Some examples](#some-examples)


### Parts
Obviously I started with a HobbyKing 3D printer (link), but to change to a laser engraver I needed to firstly get the thing working. All the steppers motors were still in good nick as far as I was aware, but I needed a control board to, well, control them, and also the laser. I also needed a laser. 

Some online research later, and I discovered RAMPS (link) and Marlin - a circuit board extension for an Arduino microcontroller, and associated firmware designed for all manner of 3d printers, laser engravers, and CNC machines. The RepRap community and forums have excellent resources around Marlin documentation, RAMPS documentation, usage, help etc. I'll give some more detail about what each of these things are:

#### RAMPS
A RAMPS 1.4 board is a 'hat' circuit board - in that it sits on top of - for an Arduino Mega, which is a circuit board that breaks out all the pins of an ATMega2560 8-bit microchip for general purpose usage. This basically means that the pins are not hardwired to any particular function, but just lead to wires so that a user can connect them to anything their program requires - like, for example, a motor control board. 

(photo)

The red board is the RAMPS board, and the blue is the Arduino - the RAMPS connects atop the arduino. It has:
+ power connections for 12v that is provided to the stepper motors and any other devices (printer bed, printer nozzle, laser, CNC spindle) and also regulated down to 5v for the microcontroller
+ connections for separate stepper motor control boards (so you can swap out exactly which stepper controllers you want; they have pros and cons). I used the X boards. There are also wiring options for how precise you need your motors to step
+ connections for the limit switches for each motor axis - these allow the device to know when the bed has travelled too far along an axis

The RepRap designers made this very simple to set up: I set the stepper accuracy to default, wired 12v power from the previous printer power supply into the power inlet, and that was the board ready to be turned on. The stepper controllers needed to have heatsinks added:

photo

and then placed in the respective spots; I use only X, Y and Z steppers, and the RAMPS board allows for X, Y, Z and two extruders, so I had some spares. 
Finally, just needed to plug in the limit switches:

photo

and we're all wired up to test the basic motion. I'll cover the laser later, but it's not particularly complicated. 

#### Marlin

#### The Laser

