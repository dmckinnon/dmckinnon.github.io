---
layout: post
title:  "Tokenisation in LLMs"
date:   2025-07-15 10:58:35 -0700
categories: Tutorials
comments: true
---


# Tokens
In a [previous post](https://dmckinnon.github.io/LLMs-overview/) I discussed how Large Language Models like ChatGPT broadly work. One important tool they use is the concept of an embedding space, where words are converted into vectors of numbers, and I wrote of how these vectors can encode and represent concepts. These vectors are then used to predict the next vector - which represents tokens in the vocabulary of the model. The vocabulary is, as the name suggests, the set of all the possible tokens - or words - that this model can use.

![image from https://jalammar.github.io/visualizing-neural-machine-translation-mechanics-of-seq2seq-models-with-attention/](/assets/llm1/embedding.png)

## Contents
- [Word tokenisation](#full-word-tokens)
- [Token basics](#token-basics)
- [Character-level tokenisation](#character-level-tokens)
- [Subword tokenisation](#subword-level-tokens)
- [Experiments](#experimenting-with-tokenisation)
- [Wrapping up](#wrapping-up)

## Full word tokens
What I had written was an oversimplification. Yes, some models do operate off whole words, but this requires a massive vocabulary; each word like 'dog' and 'dogs' would have their own vector, despite being closely related. The tokenizer wouldn't by default consider these similar. Imagine having a function that maps from every word in the dictionary to a different vector - that's huge! 

Firstly, we have a processing power problem: the model must output a probability distribution over the entire vocabulary at each step. Larger vocabularies, therefore, mean considerably more computation.

Then you would also have trouble with new words: a new mapping would be created for each new word. This is inefficient, and poorly represents words: we can't add "dog" and "s" to get the plural "dogs", since "s" isn't a token in itself. How do we get a vector with a high plural-ness dimension from a word? "s", the 'plural-maker', if you will, is not a word in itself. How would we adjust a 'dog' token vector to get the 'dogs' token vector?

Ideally, we want a system where mathematics on these vectors works on a token-meaning level too - some sort of compositional semantics. Here's an example

![image from https://jalammar.github.io/visualizing-neural-machine-translation-mechanics-of-seq2seq-models-with-attention/](/assets/tokens/vector_math.png)

This doesn't work completely if we can't have single letters like 's' as a token.

## Token basics
To overcome these difficulties and to make things work better, some language models use the concept of "tokens" instead of full words. Tokens can be whole words, can be sub words, can even be individual letters: consider a modifying suffix like 'ed' or 'ing'

If we have the token "walk", and we add the vector for the token "ing", then we should change the "tense-ness" of the vector for the token "walk" - "ing" makes this present tense (walking), vs "ed" which would make it past tense (walked). By storing sub-words like tokens, which themselves can be concepts, we can be more efficient. (eg. Many words we use are compound words of latin or greek-originating parts. Consider helicopter, misogyny, shopkeeper, and so on.). Short words, like "and", or really rare words, like erudite, might be tokens themselves, but common pieces of words - "ing", "ion", "real", etc - are made into tokens. Tokenization schemes that break words into meaningful subunits allow models to generalize better. If 'walk' and 'ing' are separate tokens, the model can learn how 'ing' modifies verbs across many contexts.

Let's go over some different token-encoding schemes and what they look like in practice. 

## Character-level tokens
One way we can represent words efficiently but also have considerable word-building flexibility, is by making each character a token: 'a', 'b', ';', '!' and so on. Then we can map characters to vectors and get 'd' + 'o' + 'g' + 's' = 'dogs'. If we added the vectors corresponding to each of these tokens, and decoded that vector, we'd get the word "dogs". Great! This is also highly memory efficient: we have a vocabulary of about 256 (all letters, numbers, a bunch of punctuation, etc)

It's also conducive to arbitrary words: as long as the word uses english characters (we could extend to other character sets too, include Iceland's eth and thorn), then it can be represented with these tokens. 

But this is not particularly powerful. As discussed, attention takes the vectors for each token and computes the relationships they all have with each other to find the most likely next vector/token. If you have a 'd' … what comes next? There's a LOT of things that could come after a 'd'. So this comes down to the data you train on. If you wanted a Language Model that only spat out, say, boys names from the 50s, then there's fewer things that come after 'd'. But all the english language? You can see the problem. 

Well, what if we just use a large context window - that is, process loads of vectors at a time? The last 50 characters probably tells us a lot about the next one character. We know from the attention discussion that a series of vectors/tokens are used, and the importance and relevance of all of them is used to compute the next likely token. 

Ok, let's consider this: so if we have 's', 'a', 'i', then the next likely token is … probably a 'd': 'said' is a common word. This is very similar to what your next-word-predictor on your keyboard does. Extending the horizon will give us more information, and so on. 

This approach has proven to work well in small, domain-specific datasets. In fact, Andrej Karpathy, eminent AI researcher, has used this token encoding scheme to make a generative Language Model to create "new shakespeare" - it generates "shakespeare-like" sentences (eg. "Forsooth, mine uncle, I beseech'd". It's not Shakespeare, but it's shakespeare-esque). This operates only on characters as tokens, using a set of 64 tokens at a time. 

But this is with all the Shakespeare to train on, which in terms of memory usage is not that much (I think 1 megabyte?) and is a heavily overfit dataset. This shows that even a simple character-level modeler can generate coherent text on a constrained domain, but will struggle to scale well. 
Attempting this on a general natural-language question-answerer or text-summariser … wouldn't produce great results (you'd need an enormous horizon, and even then characters simply don't have the expressive power). 

So does a better scheme exist? One that doesn't mean a single vector per entire word, but still contains more meaning than individual characters. 

## Subword-level tokens
As mentioned above, a lot of words we use are compound words, or made up of sub-word modifiers or building blocks. You can have a verb, walk, and this can have its tense modified with a suffix: -ed, -ing. The word 'way' is a word on its own, but can be compounded with others: gang, door, run (gangway, doorway, runway). These all relate to the original meaning of way (a space that is intended to be moved through), but modify it. A doorway implies a narrow opening; a runway implies a honking great road and aeroplanes. 

In embedding space, this would work as each of these tokens being individual vectors ('way', 'door', 'run'), and then the addition of their vectors having semantic meaning. When converted back to token space from embedding space, the sum of the vectors for 'run' and 'way' should produce a vector that translates to the tokens that make up 'runway' (perhaps a single token itself, but probably not)
To generalise this concept, by separating subwords into tokens, the model can learn how different suffices like 'ing' modify verbs across many contexts.

To highlight the advantages of this system, consider the following:

`Character: ['T','h','e',' ','c','a','t',' ','s','a','t',' ','d','o','w','n']`
`Subword: ['The',' cat',' sat', 'do', 'wn']`

In the first line, we need a much longer context length to capture this meaning. This means considerably more processing, and even with that we may not be able to express higher level meaning. In the second line, we have a far shorter context window. Here, we can perform less processing for the same meaning, or capture more context and meaning for the same number of vectors.

But how do we get these sub words? How do we decide what a reasonable sub-word is? 

One answer is: have a dictionary of predetermined tokens. Well, we considered this, and while it's mildly more reasonable than a dictionary of full words, it hits the same problems (handling new word, for example).

Another answer is: build up the vocabulary from the dataset we are given. Let's say we're trying to learn on ALL of wikipedia. And instead of coming in with a pre-made tokenisation, we're going to build our own tokenisation from the given text. 

Let's go over what this means.
We start with a simple character-based tokenisation. All lower case letters, upper case letters, all possible punctuation, spaces, even the rare semicolon and the demisemicolon (when you want but the briefest of pauses). 

Then we perform frequency analysis over the text for pairs of tokens; that is, how often does each possible pair of characters appear? For example, 'i' and 'n' would appear often together ('in' being a common word itself, but also is part of ing, a common suffix). These most common pairs get merged to create a new token: 'in' becomes a token in the vocabulary (as would things is like 'is', 'it', 'at', and so on. Not because they are words themselves, but because they are common pairings even within words, eg 'with' has 'it').

This is repeated again and again, until we have a vocabulary of a desired size. The tokens will get larger as we go, but this then means we can represent some things with fewer tokens. For example, 'tokenisation' - we might have 'token' be an entire token itself, as would 'tion', and therefore we have perhaps four or five tokens. Compare this to a per-letter basis: we can now have a denser information content in the same amount of tokens. Given that the processing scales on a per-vector basis, and each vector corresponds to one token, the denser we can be with tokens the better. 


The larger the vocabulary, the more expressive it is and the more powerful our model can be. A larger vocabulary means we can capture higher level concepts as their own vectors, for example, but in this instance we also maintain the smaller tokens that can semantically link to make new concepts. 
Even so, this approach results in a far smaller vocabulary. For context, the Oxford English dictionary reportedly contains over 170 000 words; some language models trained on natural english have a token vocabulary of only 50 000 tokens. 

Now, some big language models have zillions of tokens. But weren't we just concerned about efficiency? Well, yes. Under these tokenisation algorithms, we are still more efficient than if we used full words, etc. Some models are trained on not just english but other languages too (ChatGPT knows french, for example), and then also mathematics, emoji meaning, and also more abstract things like tokenising images. If we tried to do that with the way we commonly understand language, on a per-word level, we'd still be less efficient. 


So to summarise subword tokenisation, we can build up a vocabulary of subwords that aims to cover the most frequently used subwords, but also contains individual characters (so rare words are still representable). These subwords correspond better to the concepts representable in embedding space, like the vector for 'run' plus the vector for 'ing' being close in embedding space to the vector that gets converted back to the tokens that make up 'running'. Having tokens of varying size, and of high frequency, allows denser representations and therefore more relationships can be captured in the same-token-length context window



## Experimenting with tokenisation
Let's show tokenisation in practice. 
I trained a subword tokeniser, as described above, on the text of Shakespeare (using Karpathy's [example repository](https://github.com/karpathy/nanoGPT)), and aim for a vocabulary size of 1000 tokens. So initially we have 256 (all the single characters in a character-level tokeniser). Here's an example of some of the tokens. The numbers are their index in the vocabulary. 

    ":": 26,
    ";": 27,
    "<": 28,
    "=": 29,
    ">": 30,
    "?": 31,
    "@": 32,
    "A": 33,
    "B": 34,
    "C": 35,
    "D": 36,
    "E": 37,
    "F": 38,
    "G": 39,
Remember that for the output of the language model, it produces a vector of probabilities the size of the vocabulary, so a probability that each token could be the next one, over all tokens. The bigger your vocab, the bigger this vector and the more compute it takes. 
The tokeniser algorithm then does some frequency analysis and begins making new tokens:

    " t": 257,
    "he": 258,
    " a": 259,
    "ou": 260,
    " s": 261,
    " m": 262,
    "in": 263,
    " w": 264,
    "re": 265,
    "ha": 266,
    "nd": 267,
    " the": 268,
'the' is particularly common, since we've already merged ' t' and 'he' at this stage. It's getting to four-letter tokens before some two letter ones. This makes sense though. Why use four tokens to represent such a common word, or even two, when one will do? Now, if our language model processes a set of 8 tokens at a time, say, we can process "hey man, the" as 8 tokens, instead of 11 and therefore not fit everything in. By taking more tokens to express the same thing and not fitting necessary context in, we could miss important relationships.

Let's see more token merging:

    "one": 457,
    " pr": 458,
    " com": 459,
    " at": 460,
    " man": 461,
    "What": 462,
These are getting increasingly high level. Remember, we still want the individual character tokens in case an entirely new word/concept appears in a prompt, but "what" surely merits its own token for efficiency and power. "what" relates to a lot of things and having that in a sentence as a conceptual block makes a bigger change than just knowing those four characters are there. 


On another note, notice that a lot of letters are paired with a starting space, eg token 268 above, or token 461 just here. " the" is a very common token, with the space at the start. While "the" is almost as common, the space is the key thing - by separating that out as a token we establish the space as part of the meaning. 

    " grace": 924,
    "less": 925,
    " beg": 926,
    " eyes": 927,
    "BA": 928,
    "land": 929,
    " mother": 930,
And so on and so forth. The model trained on this vocabulary, for the same size context window and number of attention heads etc, would be more powerful and expressive than one trained on just a character-based vocab. Or perhaps best said another way: to be just an expressive, a Language model witth a character-bassed token set would need a much larger context window, and many more layers and attention heads, than a model trained with this vocabulary. 


As an aside, some models use full words from the get-go, or other Tokenizers - Hugging Face LLM Course
WordPiece tokenization - Hugging Face LLM Course
There are other tokenisation schemes, like WordPiece or Unigram, but hopefully this gives a better idea of what it means when we say "ChatGPT can operate on natural language" or what the "language" part of a Large Language Model is. These tokens are the pieces it works on, takes as input, and produces as output. 

## Wrapping up
Hopefully now you understand what tokens are, how language models understand them and use these as building blocks to produce their natural language
Different tokenisation schemes can produce very powerful models, or very weak models. Denser schemes can help a model become smaller, in a lot of ways, but have a cost (harder to produce novel language). It's similar really to how we represent language: a lot of our english words are just made up of tokens from latin and greek, and our sentences are made up of tokens of words. Yet we move these around, add them, manipulate them with ease. Language Models have just found their own way. 
