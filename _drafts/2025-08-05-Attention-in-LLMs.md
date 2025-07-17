---
layout: post
title:  "Attention in LLMs"
date:   2025-08-15 10:58:35 -0700
categories: Tutorials
comments: true
---

Add links

Add pictures




# An in-depth look at Attention Mechanisms in LLMs

I've previously written on a high level about AI, LLMs, and Tokens. This post will go over another part of LLMs in greater depth: attention. 

To recap a bunch, LLMs (Large Language Models) all use two core concepts: embedding space (tokenisation), and attention. Yes, there's more, but these are two very central and important ones. 
Words, like this text, are converted via tokenisation to vectors in a multi-dimensional embedding space, and then an operation called Attention works on these vectors to provide an output. This output is then run through the inverse process, going from embedding space to words, and this could be … an answer to a question (like CHatGPT), a summary, and so on. 

Attention is not all you need but gee it's important
The attention mechanism was introduced in a [2017 paper]() and has become ubiquitous in LLMs due to the expressive power. I've written about it [previously](), but that was the basics. Typical attention blocks have a lot more going on in them than just the basic attention formula, and this post intends to go over that.

// where does this fit?
Your basic attention head - that is, a logical block that performs attention - performs "the attention equation" on a set of vectors, and then has a few other bells and whistles to improve accuracy. A transformer then is made up of one or more layers of attention heads, concluding with a layer converting between token space and embedding space.

## Contents:

- [Core attention](#core-attention)
- [Series of Attention blocks](#series-of-attention-blocks)
- [Skip Connections](#skip-connections)
- [Normalisation](#normalisation)
- [Feed-Forward Network](#feed-forward-networks)
- [Multi-head attention](#multi-head-attention)
- [Conclusion](#conclusion)

Show the diagram of the whole attention head


## Core attention
Firstly, attention again. But cover multi-head
For own understanding, cover single head attention again

In a [previous post](https://dmckinnon.github.io/LLMs-overview/) I have gone over how attention works, and a [couple]() of [sources]() I draw from cover attention, in greater detail and with better pictures. But for the sake of a self-contained post, I'm going to go over it again. 

The input to attention is a series of vectors in [embedding space](), called the context. I'm not going to go into the details of embedding space here, as I've done it [elsewhere](https://dmckinnon.github.io/Tokenisation-in-LLMs/) and so have [others](), but this is essentially a vector space, usually a few thousand dimensions, that encodes the information for each [token](). 
The goal of attention is to use the relationships between the context vectors to predict the missing state. This missing state might be the next vector in the sequence (the next word in a sentence), it could be a gap in the middle (eg. "The scientist ____ proposed the theory of relativity", where the attention would ideally predict "Albert Einstein", or perhaps "Dr Einstein"), or it could be something more abstract, like a named entity (eg. "Los Angeles is where the next Olympics will be held", and the named entity is Los Angeles) or a summary of the given set of vectors (eg. "provide a summary of the following paragraph: …" and then attention is required to predict what this summary would be and where it would start. This does involve next-word prediction in a sentence, but it uses the attention from the given paragraph).

So how are we predicting the next vector? 

The answer is: using several matrices per vector called the Query, Key, and Value matrices. 

What are these, how do we get them, and how are they used?

### What are these matrices? 
These matrices multiply each embedding vector in the context, to give Query vectors, Key vectors, and Value vectors for each embedding vector. Consider that each of these is asking a question of the vector. 
+ The Query asks "What information is this vector looking for?" That is, it "wants" to find other vectors that strongly relate to it, instead of weakly. Therefore, what traits would such vectors have? The dot product of one vector's query with another embedding vector will produce a similarity score that shows how much the second vector contains what the first is "looking for". An example: <give example>
+ The Key asks "What information does this vector contain?" That is, each vector "knows" it needs to communicate its information (a high temporal dimension? A high locale dimension?) to other vectors to find those that are similar. 
+ The Value asks "what information can this vector communicate forwards?" This may sound similar to the Key, but it's subtly different. A vector might contain subject information, but will communicate specifics about said subject. For example, if the vector for the token 'it' is attending the vector representing 'duck', then the Key of 'duck' is "I am a potential sentence subject", and the Value is "a duck". Of course, embedding space is more abstract than this, but hopefully this highlights how this might happen. 
### How do we get these matrices?
These matrices are learned, during training. As the model is trained, these matrices converge on useful values that reveal each vector's query/key/value accurately. Said differently, at the start these matrices are blank slates, and therefore provide a very poor estimate of each vector's "true" query/key/value vectors. As the model is run during training, this error is captured and used to update the matrices until they produce results that are within an acceptable level of error.
### How are they used
Through a simple equation: Softmax(QK^T/sqrt(d))*V. (use an image)

This equation is run for each pair of embedding vectors. That is, if we have vectors v1 and v2, say, then we use the Query matrix for v1, and the Key and Value matrices for v2. Call these Q1, K2, and V2.

Let's go through this. First, we multiply Q and K, but to make this dimensionally work we use K transpose. This result, QK^T, represents the similarity between v1 and v2. How? Well, Q1 is the information that v1 is looking for. K2 is the information that v2 contains. So we must ask: how similar are these? Perhaps v1 is looking for something completely unrelated to v2. In that case, the result is a matrix of very small values - low similarity for each dimension - and therefore won't result in much. But perhaps v2 contains information across a handful of dimensions that v1 is interested in. It's not everything that v1 is after, but it's something. In that case, our resultant matrix contains some useful values. 

Next, we scale this matrix, by the inverse-square-root of the number of attention heads. Hold on, what's this? Don't worry, it's just a number, for now. This is just to ensure unit variance in the results. If we don't do this, we may get large spikes in the values, which reduces this to a one-hot encoding. Said differently, this makes the attention mechanism "absolutist" - it would either wholly believe something (a value of 1), or definitely not believe something (a value of 0), with no nuance. Scaling to unit variance allows the attention head to have nuance (values between 1 and 0), and therefore capture more than just one thing

Then the softmax. The previous step resulted in a real-valued matrix, where every cell is a number from -infinity to +infinity, potentially. This is not particularly useful. What we'd like is a probability. The softmax function takes each vector of the matrix and converts this to a vector of probabilities. Why? Well …

Now we multiply with the Value, V2. This is the information v2 can communicate. But when checking with v1, we don't necessarily want to communicate everything - that could lead to red herrings and false signals. We only want to communicate what's relevant. Remember how v2 and v1 might have similarity across a few dimensions? Well, this is where the probabilities come in. By multiplying the softmax'd matrix with V2, we weight the dimensions of V2 by the probability that each dimension is useful, or relevant. The final vector therefore is a signal for which dimensions of V2 are being attended by v1. 
		○ USE PICTURES
		○ Check this final part - does the Value produce a vector? Yes

### What do we do with all this output?
The final output of the attention block is an embedding vector - the most likely "answer" vector, or "missing" vector. To reiterate the examples from before, this could be the term missing in the middle of a sentence, this could be the next word, this could be the Named Entity, and so on. The vector produced by an Attention block is, conceptually, the "answer" to the "question" asked by the context vectors provided. In a simple example, if we gave a standard GPT the context "the cat sat on the", and it was required to provide the next vector, the resultant vector after attention would correspond (hopefully) with the token for "mat". 


## Series of Attention Blocks
TODO: explain why we need serie
Many models these days now have multiple attention blocks, chained together. 
"But why would this work? Doesn't this just do the same thing over and over again?"
Well, no. And that's where the whole "learning" part comes in. 
When undergoing the learning process, each attention head learns different layers. The early attention heads might learn simple, face-value, connections. Later attention heads might learn more high level abstract concepts. They might only tweak the embedding vectors a tiny amount, but it could be a particularly important amount. Consider the difference between an explanation of various physics concepts like gravity when you're five, when you're 15, and when you're 25 and doing your PhD. Each time it gets deeper, more complicated. Each attention head can add more depth and detail like this. They can also, in an entirely different direction, capture different data. One attention head might be focused entirely on a particular understanding, and another attention head be focused on different ideas, and one might not adjust the embedding vectors at all. It comes down to how they are trained. 
"Won't each attention block produce a separate vector? Don't we just want one vector as output?"
Well spotted. This leads nicely into the next topic, Skip-Connections

## Skip connections
We're really getting into the subtle weeds now. Taking a look at the classic attention head diagram, we can see this connection going from before the attention mechanism to after everything, with an addition. What's this?
TO understand this, understand what is conceptually happening in the attention mechanism and the FFN. We're taking the embedding vectors - most specifically, the last one, as that is the one that will be translated into token space - and modifying it based on the attention from all other vectors. 

Or consider language translation: we need all the vectors, and we may have a basic understanding (or a clean slate) of the translated text, and the attention mechanism and FFN modifies these vectors towards the correct interpretation. 

The Attention mechanism, as explained above, gives us a new vector that is the most likely "answer" based on the input context. We could end here and use this vector alone. 
BUT remember that we have a series of attention blocks? Each attention block produces its own answer vector, based on the attention Q, K and V matrices it has learned - it's "specialisations", if you will. So how can we handle each of these specialised answers? 
Well, vector math. 
If we take our original first answer, and for each block that performs attention on the context we add the answer vector to the original answer, this will "tweak" the original answer with that block's "specialisation" answer. If two blocks are trained for different details and concepts, then the vector that is the sum of their answers should encapsulate all the relevant concepts from both attention blocks. This sum answer, experimentally, is more accurate than a single attention answer vector. 

In summary, we take the original value, get the modification vector from the next attention block, and then add them together.
"But this might create some really large values in the vectors!"
Yep, it might. Luckily, we have a way to handle that. This leads nicely into the next section, Normalisation … 


## Normalisation
Either before or after (it can vary) every attention block we usually have what's called a Layernorm operation. Let's start with what this is. Layernorm is a mathematical operation that, per "layer" or across all embedding vectors being considered at a time, normalises them. That is, the mean and variance of these vectors are computed, and then each vector is offset by the mean and scaled by the variance to ensure a 0-mean, unit-std dev vector. Now, why would we do this?
Data that is distributed like this is easy to work with - for one thing, it remains numerically stable and won't "explode". This means that when computing gradients for training, or computing values for attention, no single value gets so big it dominates accumulations. 
To give an example of this, if we have vectors that contain a bunch of 5's, and one that contains a 1000, in every future operation that 1000 term is going to dominate and we lose any influence from the 5's. By normalising, the values-that-were-5s are still small but not disproportionately so and can still contribute some influence. The value-that-was-1000 is now a relatively small number, perhaps single digit, and therefore won't cause overflow or cause us to lose any smaller values to being out-of-range. 
By repeatedly doing this, we keep numbers within an easy-to-work-with range





## Feed-Forward Networks
This might be the most abstract
After normalising and performing attention between all embedding vectors, we could choose to be done. This is sufficient and can work, and can capture a lot of meaning. However, we can achieve better. Let's consider the Feed-Forward Network. 
What is this?
In essense, this node up-projects to a higher dimensional space, performs an all-to-all activation (where we have many perceptrons that take all inputs and perform a weighted sum to get an output), and then down-project to the previous dimensional space. 
That was dense. I'll go into more detail, but first, why are we doing this? This layer is non-linear in the inputs (compare to attention, which computes linear combinations of the values). This allows the model to capture non-linear relationships between embedding vectors. Since the FFN has a non linear activation function, like ReLU or GeLU, it could learn more abstract relationships between values. 
But what does this mean?
Given that we are in an abstract embedding space, this is difficult to describe. But you can imagine some concepts becoming exponentially more related, for example. A vector with a "location" value close to "Britain" attends "Macbeth" mildly. Change "location" to "Scotland", it's a bit stronger. Tweak the writing style dimension to "more archaic" and suddenly "Macbeth" is a whole lot more related. (Need a better example)
This might be more difficult to capture with a linear relationship. Possible, but more difficult. Allowing non-linear connections makes this a lot easier to capture. 

Alright, we're capturing non-linear relationships. What's the math? How does this work?
Let's break this down into steps:
1. up-projecting to a higher dimensional space
	2. The MLP
	3. Down projecting

### Up projecting:
The embedding vectors may arrive with, say, 3000 dimensions. To capture more connections and features, we expand this to a higher number of dimensions. A crude analogy mnight be "this expands each dimension into separate concepts, and allows them to separately be connected to other concepts", eg. Instead of a single location dimension this might expand into a dimension for house concept (local location), city concept (broader location), national concept (location identity), and country concept. Of course, it's more abstract than that, but hopefully this gives you an idea. 
The way this is done is with a simple matrix multiplication. If `x` is our input vector, and `x_up` is the up-projected output, then we do
`x_up = xW + b`
Where W is a matrix that is input-dimensions x output-dimensions, and b is a bias vector. 
So if x is M dimensional, and we want to go to N dimensions where N > M (say, 6000 compared to 3000), then we multiply it by this learned matrix of weights, add a learned bias vector,and this produces an N dimensional vector. The matrix multiplication means that we can weight various dimensions differently in the up-projection. Maybe for the first of the higher dimensions, we care more about lower dimensions 1 and 3? Weight them more, add a bias there. And so on.

### The MLP
MLP stands for Multi-Layer Perceptron, and this sounds more complicated than it is. What we are doing is we have a number of inputs, and a number of outputs, and each output is a weighted sum of all inputs, with an activation layer
<diagram>
So output 1 is 
output = x1*w1 + x2*w2 + … +b
All inputs, each multiplied by a weight (learned), plus a bias. 
Then we pass this output through a non-linear activation function, like ReLU or GeLU. This produces a new output value. This is the key element that allows us to capture non linear relationships, and the weighted sum for each output dimension helps capture more nuanced relationships than the input dimensions allow. You might ask "why not just have a higher dimensional space and work in that already?" Well, more dimensions means more processing time. We can find a balance between being able to do most of our processing in a smaller dimensional space for the sake of speed, and only projecting to a higher dimensional space for a partiocular operation to capture certain relationships,a nd then be done. 

### Down projecting
This is the same as up projecting, but we go from the higher dimensional space to lower. The input vector here now contains captured non linear relationships and other connections between the higher dimensions. The matrix that goes from higher dimensions to lower dimensions combines these dimensions in such a way as to retain these relationships, with a weighted sum (again, this is learned). After this, we have a vector in the original dimensional space again


## Multi-Head Attention
An attention "head" refers to a set of QKV matrices for a context-length number of vectors. Sometimes, in an attention block, there is just one attention head, and so the attention block IS the attention head, and it's simple. 
But sometimes there's more. 
Multi-head attention

## Conclusion
So to summarise: we have normalised the previous layer of vectors, to give easy number ranges to work with. We have found the attention between vectors and taken the information available (the value matrix) proportional to that attention. We've added this information onto the existing embedding vectors And now we take this total and run it through feedforward networks in a higher dimensional space to capture any non-linear relationships and other connections between the different dimensions across different vectors. These new connections are combined back into the original space. 




Hopefully this gives you a better understanding of how attention heads work in Large Language Models. It's deep, it's abstract, and it's hard to explain because honestly we just throw data at these things and hope they work. But there still is some logic behind it all. 