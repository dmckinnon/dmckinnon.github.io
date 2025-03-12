---
layout: post
title:  "AI overview"
date:   2025-02-15 10:58:35 -0700
categories: Tutorials
comments: true
---

# What is AI?
In everyday conversations, "AI" usually refers to Machine Learning, especially Neural Networks. More broadly, AI means any kind of "intelligence" from a computer, but that definition is too vague to be useful.

Machine Learning and Neural Networks refer to a subset of algorithms that - in essence - do statistical categorisation. For example, given an image, what's the probability that there is a human face at any of the pixels in the image? It looks at different parts of the image and assigns probabilities to whether a face is present. If the probability is high enough, your camera app draws a box around it.

## Why is this useful
Neural Networks are powerful becaus ethey can find hiden patterns existing datasets. Many traditional algorithms struggle with this. AI is now everywhere:

- Improving camera image quality
- Recognizing people in photos
- Cleaning up background noise in calls
- Autocorrect and sentence prediction

Most of the time, you don’t even notice it working. These small improvements make a big difference, and in more advanced cases—like ChatGPT and large language models (LLMs)—AI can even generate human-like text.

## How does this whole thing work though?
Let's go through it in broad strokes, because too much detail without context is unhelpful. 
There's several concepts to cover: training data, and spotting patterns.

### Step 1: Learning from examples

Imagine teaching someone to bake cookies without giving them a detailed recipe. You might start by showing them a few different types of cookies, giving them ingredients, and then letting them experiment. Analogously, the cookies are training data. At first, they might burn the cookies. You say "too much heat". Ok, they try again, less heat, but now they skip the flour. "Not enough flour". And so on. Eventually, they'll get close to what is a reasonable cookie. Over time, they learn from mistakes and improve. This is exactly how AI learns—by seeing examples, making guesses, and getting corrected. This specific technique is called supervised learning, since you watched and told them what was wrong each time. 

### Step 2: Recognising patterns
Think back to when you learned to recognise animals as a child. Instead of memorizing the complex biology behind every creature, you simply noticed patterns. Cats often have pointy ears and whiskers, while dogs might have a wagging tail and floppy ears, and so on. Over time, you could recognize a cat or a dog in any situation, even if the animal was seen from a different angle or had different colours. 
AI learns in a similar way. It looks at thousands of examples—images, sounds, or text—and picks up common patterns. Even if an animal is in a new setting, AI can still recognize it based on previously learned features. 

### Step 3: Making predictions
Consider your phone's camera. How does it know when a person is in the picture?
1. The algorithm was given a lot of images with boxes drawn around people, and labelled "person". A variety of angles, lighting conditions, colours, clothing, varieties of people, etc. It was also given a lot of images without people, and told there were no people. 
2. The algorithm - the AI - then takes each image and tries to predict if it has a person. It uses mathemathical operations to consider shape, colour and texture when doing this. 
3. If it gets this wrong, it accepts the feedback and updates what it considers next time. These updates are individually small, but under enough incorrect predictions, the AI will stop being wrong.

If you want more detail, it runs a mathematical operation called a convolution. This convolution pulls out more abstract features of the image – changes in colour, texture, etc. Imagine you see a blurry picture, but you can discern broad ideas like the shape of a person. The convolution creates this blurry picture that highlights these "features". Each of these "features" is then fed through What's called an activation function, that says how strong or relevant a feature is. For example, the activation function will say a blurry face is a strong feature of a person, but a smudge of green is not a strong feature. 

This can also happen with audio – you take an audio signal, run a convolution on it, which reduces the signal to abstract features like tone, high-frequency jittering, etc, and the neural network learns: these features of the audio signal are just background white noise, whereas this particular feature of the audio is someone's voice. Remove one, keep the other. 

### You do the same thing
Consider that this is basically how your brain learns. You try to ski, fail, get told "do things differently", try that new thing, notice it works better, repeat. Eventually you get good enough to get off the bunny slopes, and you try on the blue slopes and lo and behold it works!


## AI is just applied statistics
At its core, AI is a math-based system that finds patterns.
- In image recognition, it calculates the probability that an object is a person.
- In speech processing, it estimates the likelihood that a sound is human speech.
- In predictive text, it guesses the most likely next word based on past examples.

In essence, the AI is doing something simple: over all the data given, the colours, shapes and textures that are in the boxes labelled people are statistically more likely to be a person than not - based on what it has been told. All that's happening is "what's the likelihood that this blur is a person?" and maybe it's low, maybe it's high, maybe it's in the middle, and a threshold is set - "reject all people with a lower lieklihood than 80%". 
With audio, it's similar: "based on the training data, there is a 90% chance that this audio is background noise - remove it". 
Once AI is trained, it can make these predictions without human intervention.

## Wrapping up
There's a lot more to broader neural networks - feedback mechanisms, history, and so on. Some neural networks, like ChatGPT etc, are hideously complicated and rely on more arcane relations between input and intermediate values. 

But this covers the core concept: a neural network is applied statistics.
- Someone gathered data with strong statistical correlations between a signal (perhaps pixels) and a meaning/category (person).
- AI gets trained on these examples (like how we learn from experience).
- It finds patterns using mathematical operations and refines its guesses over time.

Many AI models—like ChatGPT, recommendation systems, and speech assistants—use these basic principles. Even the most advanced AI is still just a math-based pattern finder.
