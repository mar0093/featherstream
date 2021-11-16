---
title: "Introduction to LSTM"
date: 2021-11-16
weight: 40
math: true
---

An LSTM is a neural network, LSTM stands for Long Short Term Memory. They themselves are a modified version of Recurrent Neural Networks (RNN). It handles what is known as the 'vanishing gradient problem'. It is also well equipped to process, classify and predict timeseries, even when there are time lags of unknown durations.

The key difference is between an RNN and a LTSM is that a RNN is made up of only a cell, where as a LTSM is accompanied by 3 gates; input gate, forget gate and output gate.

The way an RNN works is first, it takes the X(0) from the sequence of input and then it outputs h(0) which together with X(1) is the input for the next step. So, the h(0) and X(1) is the input for the next step. Similarly, h(1) from the next is the input with X(2) for the next step and so on. This way, it keeps remembering the context while training.
The formula for the current state is:

$$h_t = f(h_{t-1} , x_t) $$

which applies the activation function like so:

$$h_t = tanh(W_{hh}h_{t-1} + W_{xh}X_{t}) $$

we can visualise this like so.

![lstm](/images/research/lstm/lstm-gates.png)

![lstm](/images/research/lstm/lstm-diagram.png)

### The Gates

You can see from the diagram above that the LSTM is created by a set of gates; input gate, output gate, forget gate.

#### Input Gates

This gate regulates the input that goes into the LTSM unit/layer. the sigmoid function, denotes which value to

### reference

https://www.simonspavound.com/posts/2020/09/equations-with-katex-in-hugo/
https://aditi-mittal.medium.com/understanding-rnn-and-lstm-f7cdf6dfc14e
https://ai.stackexchange.com/questions/18198/what-s-the-difference-between-lstm-and-rnn#:~:text=The%20main%20difference%20between%20an%20LSTM%20unit%20and,better%20the%20flow%20of%20information%20through%20the%20unit.
