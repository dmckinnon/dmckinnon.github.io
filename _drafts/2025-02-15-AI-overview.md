---
layout: post
title:  "AI overview"
date:   2025-02-15 10:58:35 -0700
categories: Tutorials
comments: true
---

## What is AI?
AI in common parlance usually refers to Machine Learning, and more specifically to Neural networks. In general, it's any "intelligence" from a computer, but this can cover such a wide variety of things that it's basically unhelpful. 
Machine Learning and Neural Networks refer to a subset of algorithms that essentially do statistical categorisation. For example, given an image, what's the probability that there is a human face at any of the pixels in the image? The algorithm will typically flag areas of high probability, and then your phone's camera app will draw a box around these areas. 
Why is this useful? Neural Networks have proven adept at predicting based off existing datasets, finding correlations that many other algorithms struggle to do, and have become ubiquitous: camera image improvement, finding people in images, cleaning up audio signals of background noise, isolating different speakers in a call, autocorrect and sentence prediction, etc. 
It's likely you don't even know they are running in your software, but they provide a lot of small improvements – and then also big improvements, with things like LLMs. 
So we want them because they improve a lot of experiences. 

### How does this whole thing work though?
Let's go through it in broad strokes, because too much detail without context is unhelpful. 
There's several concepts to cover: training data, and spotting patterns.

Imagine you're teaching a friend how to bake cookies without overwhelming them with a recipe filled with technical details. You might start by showing them a few different types of cookies and explaining that each cookie is made by combining simple ingredients in different ways. Analogously, the cookies are training data, and you're training your friend – the AI – to bake cookies. You show them a cookie, they attempt to bake it from ingredients, and it looks awful. You say "too much heat". Ok, they try again, less heat, and make another mistake. "Not enough flour". And so on. Eventually, they'll get close to what is a reasonable cookie. All these cookies that you showed them are the training data, and this is called supervised learning, because you watched and told them what was wrong. 

Think back to when you were a child learning to recognize animals. Instead of memorizing the complex biology behind every creature, you simply learned that cats often have pointy ears and whiskers, while dogs might have a wagging tail and floppy ears. Over time, you could recognize a cat or a dog in any situation, even if the animal was seen from a different angle or in a different setting. Similarly, AI systems look at lots of examples—be it pictures, numbers, or words—and gradually learn to spot the patterns that distinguish one thing from another.
Sometimes you get it wrong in the real world, and get corrected (or don't). But you learn the general patterns. 

Consider your phone. It can recognise when a person is in the camera image. How does this work? The algorithm was given a lot of images with boxes drawn around people, and labelled "person". Lots os angles, lighting conditions, colours, clothing, varieties of people, etc. It was also given a lot of images without people, and told there were no people. 
The algorithm then takes each image and tries to predict – does it have a person? It runs a mathematical operation called a convolution – let's not focus on the details here – and this convolution pulls out more abstract features of the image – changes in colour, texture, etc. 
Imagine you see a blurry picture, but you can discern broad strokes like the shape of a person – this is basically what is happening. The convolution creates this blurry picture that almost highlights these broad strokes in a way. Each of these is then fed through an "activation function" - again, don't stress about the details – which will give a higher value for something that looks more like a person. 

How does it know? The training. The first time it tries, it'll be rubbish. It says "this isn't a person", when it is. The trainer says "nah it is mate", and so the neural network then updates – it nudges a lot of the weights – the broad strokes it considers important – such that it's more likely to consider that a person. 
Repeat process.

After thousands of such examples, it's drastically better, and you can test it on a fresh set of images. 

Consider that this is basically how your brain learns. You try to ski, fail, get told "do things differently", try that new thing, notice it works better, repeat. Eventually you get good enough to get off the bunny slopes, and you try on the blue slopes and lo and behold it works!

This can also happen with audio – you take an audio signal, run a convolution on it, which reduces the signal to abstract features like tone, high-frequency jittering, etc, and the neural network learns: these features of the audio signal are just background white noise, whereas this particular feature of the audio is someone's voice. Remove one, keep the other. 

What is happening here statistically is that across all the data the network is given, the groups of features that make it say this is a person, that "blurry image", are statistically more likely to be a person than not - based on what it has been told. All that's happening is "what's the likelihood that this blur is a person?" and maybe it's low, maybe it's high, maybe it's in the middle, and a threshold is set - "reject all people with a lower lieklihood than 80%". 
With audio, it's similar: "based on the training data, there is a 90% chance that this audio is background noise - remove it". 
With people, it's similar, if perhaps implicit: "there's a 95% chance that that is a cookie in their hand, and they intend for me to take it". You take it, and hopefully you are right. 

There's a lot more to broader neural networks - feedback mechanisms, history, and so on. Some neural networks, like ChatGPT etc, are hideously complicated and rely on more arcane relations between input and intermediate values. 

But this covers the core concept: a neural network is applied statistics. Someone gathered data with strong statistical correlations between a signal (perhaps pixels) and a meaning/category (person) and this is an algorithm that uses some simple linear algebra (multiplying pixel values by other numbers, that's all that happens in a convolution)
They're a lot simpler when broken down like that.
