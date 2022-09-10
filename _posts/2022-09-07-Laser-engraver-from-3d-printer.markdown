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

- [Wiring](#wiring-and-firmware)

- [Final product](#final-product)

- [Some examples](#some-examples)


### Parts
Obviously I started with a HobbyKing 3D printer (link), but to change to a laser engraver I needed to firstly get the thing working. All the steppers motors were still in good nick as far as I was aware, but I needed a control board to, well, control them, and also the laser. I also needed a laser. 

Some online research later, and I discovered RAMPS (link) and Marlin - a circuit board extension for an Arduino microcontroller, and associated firmware designed for all manner of 3d printers, laser engravers, and CNC machines. The RepRap community and forums have excellent resources around Marlin documentation, RAMPS documentation, usage, help etc. I'll give some more detail about what each of these things are:

#### RAMPS
A [RAMPS 1.4 board](https://reprap.org/wiki/RAMPS_1.4) is a 'hat' circuit board - in that it sits on top of - for an Arduino Mega, which is a circuit board that breaks out all the pins of an ATMega2560 8-bit microchip for general purpose usage. This basically means that the pins are not hardwired to any particular function, but just lead to wires so that a user can connect them to anything their program requires - like, for example, a motor control board. 

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
Marlin is open-source firmware the whole class of 3d printers/laser engravers/CNC machines that can run on a multitude of boards, including Arduino. It's well documented, well supported, well used, so it seemed like an easy and obvious choice. This is what will read from the SD card, and convert from the gcode (the instructions on how to print/engrave etc) to actual motor movements, laser strength, etc. 

This needed to be configured specifically for my setup, in that I needed to tell it I had a laser (instead of a 3d printer extruder, or a CNC spindle), which pin powered the laser, and so on. Here are the changes I made to the code:

(code)

There are others, like LASER_MODE_MOVEMENT or whatever, and I spent quite a while poking around changing things trying to get all this working, before just deciding I wanted _anything_ working. So instead of the gcode instruction M4 turning the laser on, this uses the G0 and G1 commands with an S parameter, eg. G0 S255 X1 Y1 will move the head to an x,y coordinate of 1,1 (in mm) with the laser at maximum power (it has an 8-bit power range, so 0-255). This means I have to be careful about the gcode I generate, as if it uses M4 for laser power, then the gcode may not work. 

#### The Laser
This was a 405nm 1.5W laser, which is a dangerously bright blue - it came with laser safety goggles:

photo

Wiring this was fairly simple, all it needs is power. I wired this to D10 on the RAMPS board, and updated the software appropriately:

photo

and it was good to go! For a while I thought this was powered only by the fan instructions, since these pins are for fan power, and I also had it wired to D9 - which on my board was faulty, turned out. D10 and the right instructions sorted this out:

photo

### Building a case
Laser engravers generate a whole lot of nasty fumes, so I wanted to build a case around this with an exhaust vent so I could direct those fumes away or at least catch them in a filter. Furthermore, this laser is far too dangerous to look at with the naked eye - so either it can sit in its own room and I remember to always wear laser glasses upon entering ... or the case could have laser glass on the front with opaque sides, and it's safe to use. The latter sounds easier and works well with the fumes idea. I got a large piece of thin wood and 3D printed some brackets to bolt the whole frame down to the wood:

photo

Was it big enough? I figured that the wood only needed to be as big as the printer's base area. Sadly, I forgot to account for the plate potentially moving off the far end, and also only measured from the base - and the Y stepper juts out the side a little. So a couple of the walls either need to be angled, or have holes - at least the Y stepper is static, and I can air-seal around it. The plate was a bigger problem, but unfortunately one I did not think of at the time. Moving on, I screwed the power supply onto one corner, and the control electronics and screen onto the other, with a power switch - the power supply was on/off from the plug, and I wanted to leave this plugged in but off. My intention was to keep the screen exposed - so the case would bypass it, as a user needs to access the screen, the control knob, and also the SD card slot - and also keep the arduino near the edge, so the USB port could be accessed for reprogramming just in case. In retrospect, I should have angled the screen about 30 degrees from flat. I had thought that a user would always be above the engraver, and looking down rather than at an angle is easier, but then I later realised I did not know the height, and if this was on a shelf at shoulder height, some angle would make life a lot easier. 

(photo)

Maybe 3D print an angle mount for screen. 

Laser glass, carboard sides, fan. 

### Wiring and Firmware
I broke this down into several stages. Firstly, I needed to check that the new control board could at the very least move the motors. Lasers turn on and off - they're simple. Motors have to be calibrated, have to go the expected direction, and each motor needs to be connected to the expected axis output. Secondly, the limit switches to stop the motors going too far. Thirdly, the laser. The simplest of the wiring, but hardest part of the firmware. Finally, consistent power to the board and to fans. 

#### Motors
