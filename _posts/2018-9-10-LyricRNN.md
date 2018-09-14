---
title: LyricRNN
author: Peer
layout: post
---

# Intro
Lyric RNN is a Recurrent Neural Network which generates lyrics. I have trained it on Hip-Hop, so be warned the content following may be explicit. Here is a small song I made (It is explicit)

<iframe width="100%" height="300" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/494755635&color=%23ff5500&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true&visual=true"></iframe>

This post more acts like a redirection to resources and my code for LyricRNN.

# What exactly are RNNs

I will assume, that you allready know what a standard feed forward neural net is. So let's just start by talking about why you would want a RNN instead of a NN. 

**Recurrent** Neural Networks have one minor advantage over standard neural networks. They can work with sequences of data. Guess what, a text is a sequence and lyrics are text. This means we know that we will use a RNN as our model.

However there different types of RNNs. There are Standard RNNs and there are LSTMs. I will not talk about theory, because many others allready have done. I recommend [Andrej Karpathys Blog post on RNNs](http://karpathy.github.io/2015/05/21/rnn-effectiveness/) and [Christopher Olah's blog post on LSTMs](http://colah.github.io/posts/2015-08-Understanding-LSTMs/). With this being said, let's get to the fun part.

# How to generate Lyrics

I have created a simple Jupyter Notebook, you can find on [this page](https://github.com/peerlator/LyricRNN/blob/master/Tutorial.ipynb), to guide you. I recommend you download the data and then use [Google Colab](https://colab.research.google.com/) and import the data from you're machine. This avoids the hassle of setting up and deeplearning enviroment and you can get a GPU for training.