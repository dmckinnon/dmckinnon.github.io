---
layout: post
title:  "ML Business Card"
date:   2025-04-01 10:58:35 -0700
categories: Projects
comments: true
---

# Machine Learning on a Business card 

I've seen blog posts of business cards that run linux - [exhibit A](https://www.thirtythreeforty.net/posts/2019/12/my-business-card-runs-linux/), [exhibit B](https://dmitry.gr/?r=05.Projects&proj=33.%20LinuxCard) - and as a tinkerer and someone involved in embedded systems, this absolutely tickles me. How cool! Think about what this says of you, immediately!

I really wanted to make a business card like this - specifically, one with a microchip, embedded software, and some fun functionality. But I'm not really an embedded OS person, more of an embedded ML person. Can we run embedded machine learning inference on a business card?

## Contents
- [Idea overview](#the-idea)

- [A prototype](#a-prototype)  

- [Circuit Design](#designing-the-circuit)

- [Manufacturing](#manufacturing-the-circuit)

- [Final product](#final-product)

## The Idea
Let's go over what this would entail. I want a circuit board the size of a business card (about 2" x 3", or 50mm x 80mm),
with my details printed on it. It needs to have a circuit that uses a microchip, and this microchip must run a
machine learning algorithm - specifically, a neural network.
The neural network should take some input from the user, and give some feedback that it was successful. It also should be powered by USB, for convenience.

I know from the [examples](https://www.thirtythreeforty.net/posts/2019/12/my-business-card-runs-linux/) [above](https://www.thirtythreeforty.net/posts/2019/12/my-business-card-runs-linux/)
that this is possible. That is, a business-card-sized circuit board with a microchip powered by USB is do-able. The question is: what neural network can we run on such a tiny chip?

### Neural networks
Before going further, let's discuss [neural networks](https://www.ibm.com/think/topics/neural-networks). If you already know about them, jump ahead. If you don't this is
not a comprehensive explanation - they're a dense topic. This is just an overview. A neural network is an algorithm that performs
statistical categorisation. Or, in other words, AI. It takes some input like an image or an audio stream, finds some notable pattern within like a collection
of pixels that resemble a face, or a collection of frequencies and amplitudes that sound like "hello", and assign a score to a number of categories. 
These might be: face, ball, apple, dog. They could be spoken words: hello, goodbye, hey google. A high score for a particular category denotes confidence
that "this is it". 

Typically, however, neural networks are computationally intensive - that is, they require a lot of time and mathematics to arrive at an answer. Big powerful processors do this with ease.
Tiny microprocessors that fit onto business cards ... less so. The neural network needs to be minimal.

A speech recognition neural network sounds like a good idea - this would recognise a set of spoken words,
and somehow display which word was spoken to the user. The input here would be a brief snippet of audio. The advantage is that this takes up a lot less memory than, say, an image from a camera. Correspondingly,
such neural networks tend to be smaller - especially when they do not detect many words.
Microphones can be really small, on the order of millimetres. I can use LEDs to display 
the result in some way - maybe just recognise numbers spoken, like "one", "two", etc, and display the number. 


### Making my own neural network
Some research uncovered [this tutorial](https://www.allaboutcircuits.com/projects/tinyml-projects-creating-a-voice-controlled-robot-subsystems/).
This is a speech recognition neural network model, that is run on an Arduino (read: tiny microprocessor). Perfect! Even better is that the tutorial
demonstrates how to train the model with new data, and the training data gives a bigger selection of words to choose from. These include the numbers 'one' to 'nine'. 
I selected these numbers for the model to detect, ran through the training tutorial, and bam - a small neural network model that recognises a few spoken numbers.

I acquired the [Arduino used in the tutorial](https://docs.arduino.cc/hardware/nano-33-ble/) - the board comes with a microphone on it - uploaded the model and the surrounding software, and ran it.
It worked with perhaps ~70% accuracy (70% of the time it correctly identified the number I'd spoken, and displayed it on my computer). This is great! An excellent demonstration that can catch attention. Next step is to design this into a business card.


## A prototype

I have the working concept on a working circuit board. So why make something new?
Why not just completely reuse this in my own design? It'll fit within a business card (3" x 2" approx) easily.
The [circuit board](https://docs.arduino.cc/hardware/nano-33-ble/) I'm using is about 45mm x 15mm.
Arduino even open-sources their hardware, providing [all schematics](https://docs.arduino.cc/resources/schematics/ABX00030-schematics.pdf?_gl=1*9iuc9m*_up*MQ..*_ga*MzE1NzM3NzU2LjE3NDEzMDk2NDM.*_ga_NEXN8H46L5*MTc0MTMwOTY0MC4xLjAuMTc0MTMwOTY0MC4wLjAuMjA3NzcwODg4OQ..).
What's stopping me?

One main reason: the microchip used, the nRF52840, is a [BGA](https://en.wikipedia.org/wiki/Ball_grid_array) chip.
This means that instead of having little legs on the sides to solder the chip down, it has dots underneath. 
I've [written about this in more detail elsewhere](https://dmckinnon.github.io/SMD-components-are-hard/) - suffice to say for here that such microchips
are not for novices, and I am still a novice. I need a different microchip.

### The RP2040
This is a microchip [designed by Raspberry Pi](https://www.raspberrypi.com/products/rp2040/). Important features here include:
- Quad Flat package! This is the opposite of BGA - this has little legs on the side to solder. This is much kinder to a novice.
- nominal 125MHz dual core Cortex M0
    - Read: twice as fast as the nRF52840, which we know runs the software. So this is easily fast enough. 
- 520Kb RAM
    - The neural network is 46kB, the rest of the software ~200kB, so this is easily enough
- Programming it is really easy
- well documented and supported

Downsides:
- The software I have that I know works was written for the nRF microchip. I can rewrite it for the RP2040, it just takes time. 

The upsides were enough to overcome the (minimal) downside. I acquired a [dev board](https://www.raspberrypi.com/products/raspberry-pi-pico/) for this chip,
ported the software, and got the tutorial I had before running. I also added extra software to display the numerical result on some LEDs wired to the board.
This was not without issue, however. On this new chip, the neural network showed to be ~30% accurate, with a heavy bias towards recognising 'eight'. 
In fact, 'eight' was a false positive _most of the time_. What's wrong?

### The differences
Let's consider the differences between the two setups. The arduino setup works great, the RP2040 setup does not. They have some differences.
Correlation does not _imply_ causation, but it does sidle up to it at parties or wink at it suggestively across a crowded room.

What are the differences?

- dual core microchip for RP2040 vs single core microchip for nRF
    - doubt it's this, unless the software is written really badly
- different software?
    - checked, it's basically the same barring minor low level calls that are not in critical areas (except for PDM but we'll get there)
- different model?
    - neural network is the same bits for each, both using TFLite micro. I copy-pasted.
- different vocal input?
    - I'm speaking into each, and I recorded myself - sounds the same to my ear.
- different audio input?

Ah, here we go. The nRF chip has hardware PDM circuits to read data from the microphone, but the RP2040 uses PIO to achieve this. A difference!
Let's go into more detail.


#### PDM
Pulse Density Modulation encodes analog signals into a digital bitstream (a sequence of bits sent at a certain frequency) where the number of high pulses (a '1') over a period
signifies the signal's amplitude. That is, the density of pulses represents amplitude, and this density is modulated. This bitstream
must be far higher frequency than the original signal, as it needs many bits to represent the amplitude at a certain time point.
That is, if the analog signal amplitude over a particular 5 millisecond sample (200Hz sampling) is approximately 0.5, then the bitstream needs to send 16 ones and 16 0s
within 5ms ... which requires 32*200Hz == 16kHz bit rate.
Inn both chips' cases, a ~1MHz bitstream is used, and the human voice peaks at ~20kHz (don't quote me on that).
The analog signal can be recreated by using a low-pass filter that integrates the bitstream over time to produce a multi-bit value
to represent amplitude at that time.

The nRF chip [implements this in hardware](https://docs.nordicsemi.com/bundle/ps_nrf52840/page/pdm.html).
This means that it has an electronic circuit in the silicon that is designed for converting a PDM signal from the microphone into a series
of values that represent amplitudes at times. There is also a circuit for performing the decimation filter to
convert the bitstream to the multi-bit values.

#### PIO
Programmable IO is a feature on the RP2040 that allows a simple assembly language to control state machines on various IO pins.
These state machines can be configured for things like serial data shifting, clock generation, etc. In this case, one pin is
configured to be the PDM clock, and another pin is configured as the input, shifting in one bit per clock cycle. This bits are
then pushed in sets of 32 to a buffer. Any further filtering, etc, runs in software. 

The nRF state machine could have a slightly different clock signal.
It could have a slightly different shifter. It might do an extra step before pushing bits further.
The hardware decimation filter has parameters and coefficients that are hidden. All these things can produce slightly different sounds,
which a neural network can interpret differently.

#### What can be done?
I'm still investigating this part, unfortunately. Stay tuned. 

For now, 30% accuracy is enough. Let's get on with designing the rest of the circuit.

## Designing the circuit
Like Arduino, Raspberry Pi open sourced the dev board I used, the [Raspberry Pi Pico](https://www.raspberrypi.com/products/raspberry-pi-pico/).
It's relatively simple - power regulation, flash memory, the chip itself, an LED, a crystal, some passive components, and all other pins broken out to
the edges of the physical board. 

What I need here is: power regulation, flash memory, the chip itself, more LEDs, a crystal, and the PDM microphone.
This should not be too difficult, especially given that the documentation with the chip provides a sample circuit, including the KiCAD files. 

### KiCAD
KiCAD is a program for electronic circuit design. There are two main interfaces I care about: schematic, and the PCB.

The schematic lays out the theory of the circuit: which components are used, what are their connections. So I add the chip, power, flash memory, leds, etc and connect as needed.
Here's an example:

![](/assets/card/kicad_schematic.png)

Here we see the flash memory for the RP2040. I'd copied this straight from the examples Raspberry Pi provided.
Several pins are pulled high or low (3.3v or ground respectively), some have decoupling capacitors, one is connected to a switch - this gets pulled
low to program the device. Several are connected to labels that are used elsewhere. The full schematic is [here](https://github.com/dmckinnon/dmckinnon.github.io/blob/master/assets/card/schematic.pdf).

Then there is the PCB design. This is the physical layout that will be on the actual board.
First, the outline of the board is created from the edge.cuts layer:

![](/assets/card/kicad_pcb_cut.png)

This has the dimensions of a business card (~80mm x ~50mm), but one corner is cut out for a USB-C insert. Most of the rest of the board is unused by the circuit and I just filled it with business card text: name, email, description, and instructions on card usage. 
Why is the circuit crammed into one side and not more spread out? Well, firstly spread out would make the text arrangement harder - I don't want components and text mixed, for readability. Secondly, short tracks benefit small circuits - or rather, long tracks are detrimental. Extra resistance, etc. So, let's cram it tiny. 

The board is 0.8mm thick, which is the thickness inside a USB "male" connector. Male is in quotes because technically the "female" half of USB has a "male" part in it too (is it non binary?). This board acts as the outtie bit in the female connector. Let's look at the USB-C part:

![](/assets/card/kicad_usb_c.png)

![](/assets/card/card_in_usb.png)

![](/assets/card/card_usb.png)

There are tracks only on one side since the circuit only need be one side.
The tracks used are the ones necessary for USB 2.0 mode, as I only need this for data transfer - to program and debug - and thereafter for power.
Thankfully, this worked first go. As a backup, the first circuit had a place for an on-board USB connector, but this went unused for later versions (See [below](#mistakes-along-the-way))

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

### Mistakes along the way
The circuit design had three distinct iterations, due to several mistakes. 

The first design I connected the Chip Select pin for the Flash Memory incorrectly - it was always pulled high, so I couldn't use the button
to select "write your program to memory" mode. Whoops. 

Second iteration corrected this, but kept another mistake that had been in the first design and I just hadn't spotted it due to the first error.
There are several pins on the RP2040 that require 1.1v power for the CPU. There is another pin that supplies this power, and these pins all need
to be externally connected. I had misread the reference design I was using, and missed these connections. This meant, essentially, that the CPU was
not powered. No power means no speech recognition. 

So even when copying from a reference design mistakes can be made. Both these points were highlighted in the documentation too, and the biggest mistake
was probably not readin ghtis in entirety first. If there is good documentation ... read the documentation.

## Manufacturing the circuit
PCBWay manufactured the board for $15 for ten boards, using USPS shipping (~5 for the boards, ~9 for shipping). Mouser provided the parts, at varying costs. Unfortunately this is quite an expensive board, for a couple of reasons:
- the flash chip isn't cheap
- the buckboost converter for power regulation isn't cheap
    - this could absolutely have been made cheaper, I could use a linear regulator. They are less efficient, but this is not battery powered so that does not matter.
- the microphone is not cheap (over a dollar per mic!)

All in all, the cost is perhaps ~$12 a board? Pricey, when compared to some of the Linux business cards I saw online (one was at $3 a board). Ah well, I'll design the next one with price in mind. If one of these got me a job, $12 is well worth it.

I soldered the boards myself - [Josh Elsdon](https://www.elsdon.io/) is a friend and has a SMD soldering setup. A controllable-pressure heat gun, and a soldering iron with a _really_ fine tip, plus solder paste and flux. 

![](/assets/card/soldering.png)

Definitely the hardest thing I have soldered, but it got easier with practice. See [this](https://dmckinnon.github.io/SMD-components-are-hard/) for my difficulties with the microphone. 

## Final Product
And yet I got it working!

![](/assets/card/final.png)

Can't exactly have a video on this page, but it's largely working. There's still the software issues mentioned in [the differences](#the-differences) - this leads to eight being heard far more often than anything else, and some numbers not being heard at all. It's a work in progress. 





