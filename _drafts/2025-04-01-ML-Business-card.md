---
layout: post
title:  "ML Business Card"
date:   2025-04-01 10:58:35 -0700
categories: Projects
comments: true
---

# A Business card 

I've seen blog posts of business cards that run linux - [exhibit A](https://www.thirtythreeforty.net/posts/2019/12/my-business-card-runs-linux/), [exhibit B](https://dmitry.gr/?r=05.Projects&proj=33.%20LinuxCard) - and as a tinkerer and someone involved in embedded systems, this absolutely tickles me. How cool! Think about what this says of you, immediately!

I really wanted to make one. But I'm not really an embedded OS person, more of an embedded ML person. Can we run embedded ML on a business card?

## Contents
- [The Arduino](#the-arduino-nano-ble-33)  

- [Chip Selection](#can-i-make-my-own)

- [Circuit Design](#designing-the-circuit)

- [Manufacturing](#manufacturing-the-circuit)

- [Final product](#final-product)

## The Arduino Nano BLE 33
Turns out that you can! Following [this tutorial](https://www.allaboutcircuits.com/projects/tinyml-projects-creating-a-voice-controlled-robot-subsystems/), we have an example of a speech recogniser model running on an Arduino Nano. The chip specifically is an nRF-somethingsomething, which has a single-core ARM Cortex M4 clocked at 64MHz. I followed the tutorial, adding in my own target words - the model now detects 'one' through to 'nine' - and this worked on the Arduino board with perhaps 70% accuracy. That is, about 70% of the time it detected and correctly categorised the word I said, printing this out via serial. 

## Can I make my own?
Arduino open-sources their hardware, providing [all schematics](https://docs.arduino.cc/resources/schematics/ABX00030-schematics.pdf?_gl=1*9iuc9m*_up*MQ..*_ga*MzE1NzM3NzU2LjE3NDEzMDk2NDM.*_ga_NEXN8H46L5*MTc0MTMwOTY0MC4xLjAuMTc0MTMwOTY0MC4wLjAuMjA3NzcwODg4OQ..) - from this I can see which components are used and how they are connected. For example, the microphone used is an MP34TDR PDM microphone. They also open-source all their example code, so I was able to just access this directly. 

### The nRF52840
While I did design the entire circuit around the nRF, I'll talk about the rest of the circuit later. Let's discuss just the usage of this chip first.

There are several features of this chip that make it desirable here:
- sufficient internal flash (any program with tflite micro is gonna be chunky)
- built-in USB data lines (no need for an FTDI chip anymore, woo!)
- built-in hardware PDM circuit (it's important, just accept this for now)
- the code just works and I know this

However, the nRF phyiscal chip package is BGA only. What does this mean? It means that there are pins buried underneath the chip itself that I cannot access from the outside, meaning I just have to trust they are soldered. This is above my skill and tools. [SMD components are hard](https://dmckinnon.github.io/SMD-components-are-hard/).

So let's pick another chip: the RP2040

### The RP2040
This is a microchip [designed by Raspberry Pi](https://www.raspberrypi.com/products/rp2040/). Important features here include:
- GPIO ports with PIO interfaces (programmable hardware not too unlike an FPGA)
- nominal 125MHz dual core Cortex M0 (so definitely fast enough)
- 520Kb RAM (why 520 not 512? This always bugged me)
- built in USB data lines
- well documented and supported
- it's cheap and so are the dev boards
- Quad Flat Package, not BGA, so easier to solder on

Downsides:
- I have to port the software

There's a bunch of software issues here, things like the Arduino TFLite Micro uses mbed OS and I wanted to avoid that on RPi because ... why have it, and the example used FFT libraries from a custom TFLite micro that I needed to bring across etc etc etc these details are boring. 

Point is, I ported the software to a Raspberry Pi Pico dev board with this chip, and a breakout board with the microphone connected. It worked. Not as well (~30% accuracy???) but it worked. I'll stress about those errors later. 

### The differences
So this dev board setup was different from the arduino, and also had a lower accuracy. Correlation does not _imply_ causation, but it does sidle up to it at parties or wink at it suggestively across a crowded room.

What are the differences?

- dual core vs single core
    - doubt it's this, unless the software is written really badly
- different software?
    - checked, it's basically the same barring minor low level calls that are not in critical areas (except for PDM but we'll get there)
- different model?
    - neural network is the same bits for each, both using TFLite micro 
- different input

Ah, here we go. So the nRF chip has hardware PDM circuits, and the RP2040 uses PIO to do the same. 

First, let's briefly discuss these three things:

#### PDM
Pulse Density Modulation encodes analog signals into a digital bitstream where the number of high pulses (a '1') over a period signifies the signal's amplitude. That is, the density of pulses represents amplitude, and this density is modulated. This bitstream must be far higher frequency than the original signal - in both chips' cases, ~1MHz is used, and the human voice peaks at ~20kHz (don't quote me on that).
The analog signal can be recreated by using a low-pass filter that integrates the bitstream over time to produce a multi-bit value to represent amplitude at that time.

The nRF chip [implements this in hardware](https://docs.nordicsemi.com/bundle/ps_nrf52840/page/pdm.html). This means that it has an electronic circuit in the silicon that is designed for converting a PDM signal into a series of values that represent amplitudes at times. There is also a circuit for performing the decimation filter to convert the bitstream to the multi-bit values, 

#### PIO
Programmable IO is a feature on the RP2040 that allows a simple assembly language to control state machines on various IO pins. These state machines can be configured for things like serial data shifting, clock generation, etc. In this case, one pin is configured to be the PDM clock, and another pin is configured as the input, shifting in one bit per clock cycle. This bits are then pushed in sets of 32 to a buffer. Any further filtering, etc, runs in software. 

The nRF state machine could have a slightly different clock signal. It could have a slightly different shifter. It might do an extra step before pushing bits further. The hardware decimation filter has parameters and coefficients that are hidden. All these things can produce slightly different sounds, which a neural network can interpret differently.



## Designing the circuit
Like Arduino, Raspberry Pi open sourced their dev board, the [Raspberry Pi Pico](https://www.raspberrypi.com/products/raspberry-pi-pico/). It's relatively simple - power regulation, flash, the chip itself, an LED, a crystal, some passive components, and all other pins broken out to the edges of the physical board. 

What I need here is: power regulation, flash, the chip itself, more LEDs, a crystal, and the PDM microphone. This should not be too difficult, especially given that the documentation with the chip provides a sample circuit, including the KiCAD files. 

### KiCAD
KiCAD is a program for electronic circuit design. There are two main interfaces I care about: schematic, and the PCB.

The schematic lays out the theory of the circuit: which components are used, what are their connections. Here's an example:

![](/assets/card/kicad_schematic.png)

Here we see the flash memory for the RP2040. I'd copied this straight from the examples Raspberry Pi provided. Several pins are pulled high or low, some have decoupling capacitors, one is connected to a switch - this gets pulled low to program the device. Several are connected to labels that are used elsewhere. See the full circuit as a pdf in the repo (LINK).

Then there is the PCB design. This is the layout. Having chosen components and connections, now they are physically laid out. First, the outline of the board is created from the edge.cuts layer:

![](/assets/card/kicad_pcb.png)

This has the dimensions of a business card (~80mm x ~50mm), but one corner is cut out for a USB-C insert. We'll get back to this. Most of the rest of the board is unused by the circuit and I just filled it with business card text: name, email, description, and instructions on card usage. 
Why is the circuit crammed into one side and not more spread out? Well, firstly spread out would make the text ararngement harder - I don't want components and text mixed, for readability. Secondly, short tracks benefit small circuits - or rather, long tracks are detrimental. Extra resistance, etc. So, let's cram it tiny. 

The board is 0.8mm thick, which is the thickness inside a USB "male" connector. Male is in quotes because technically the "female" half of USB has a "male" part in it too (is it non binary?). This board acts as the outtie bit in the female connector. Let's look at the USB-C part:

![](/assets/card/kicad_usb_c.png)

![](/assets/card/card_in_usb.png)

![](/assets/card/card_usb.png)

There are tracks only on one side since the circuit only need be one side. The tracks used are the ones necessary for USB 2.0 mode, as I only need this for data transfer - to program and debug - and thereafter for power. Thankfully, this worked first go. As a backup, the first circuit had a place for an on-board USB connector, but this went unused. 

Next, we arrange the components. The RP2040 is biggest and central, but the tracks around it should be sensibly arranged. For example, the flash chip uses the pins on the top side of the RP2040 - so let's place it there:

![](/assets/card/kicad_flash.png)

I made sure the LEDs used pins on the right side, as the crystal used some of the bottom pins:

![](/assets/card/kicad_leds.png)

The pins for the PDM microphone are on the left:

![](/assets/card/kicad_mic.png)

and around it are decoupling capacitors. 

The LEDs are arranged in a 7-segment display configuration:

![](/assets/card/kicad_more_leds.png)

Originally I had 4, and would display the result in binary. Someone pointed out that 7-segment and decimal was just ... clearer. Less ambiguous and confusing. Fair point, and I do have GPIO pins to spare. 

This entire circuit and process went through several iterations, as I made mistakes, forgot connections, redesigned, and so on. This is the final design:

![](/assets/card/kicad_pcb.png)

## Manufacturing the circuit
PCBWay manufactured the board itself for $15 for ten boards, using USPS shipping. Mouser provided the parts, at varying costs. Unfortunately this is quite an expensive board, for a couple of reasons:
- the flash chip isn't cheap (cost)
- the buckboost converter for power regulation isn't cheap (cost)
    - this could absolutely have been made cheaper, I could use a linear regulator. They are less efficient, but this is not battery powered so that does not matter.
- the microphone is not cheap

All in all, the cost is perhaps ~$12 a board? Pricey, when compared to some of the Linux business cards I saw online (one was at $3 a board). Ah well, I'll design the next one with price in mind. If one of these got me a job, $12 is well worth it.

I soldered the boards myself - [Josh Elsdon](https://www.elsdon.io/) is a friend and has a SMD soldering setup. A controllable-pressure heat gun, and a soldering iron with a _really_ fine tip, plus solder paste and flux. 

![](/assets/card/soldering.png)

Definitely the hardest thing I have soldered, but it got easier with practice. See [this](https://dmckinnon.github.io/SMD-components-are-hard/) for my difficulties with the microphone. 

## Final Product
And yet I got it working!

![](/assets/card/final.png)

Can't exactly have a video on this page, but it's largely working. There's still the software issues mentioned in [the differences](#the-differences) - this leads to eight being heard far more often than anything else, and some numbers not being heard at all. It's a work in progress. 





