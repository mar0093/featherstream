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

$$ h_t = f(h_{t-1} , x_t) $$

which applies the activation function like so:

$$ h_t = tanh(W_{hh}h_{t-1} + W_{xh}X_{t}) $$

we can visualise this like so.

![lstm](/images/research/lstm/lstm-gates.png)

Note the following notation

- \\(W\\) is the weight.
- \\(h\\) is the single hidden vector.
- \\(W_{hh}\\) is the weight at the previous hidden state.
- \\(W_{hx}\\) is the weight at the current input state.
- \\(tanh\\) is the *activation function*, which squashes the activations range to between \\([-1,1]\\)

This means that our output is produced as:

$$ y_t = W_{hy}h_t $$

Where:

- \\(Y_t\\) is the output state
- \\(W_{hy}\\) is the weight at the output state.


![lstm](/images/research/lstm/lstm-diagram.png)

### The Gates

You can see from the diagram above that the LSTM is created by a set of gates; input gate, output gate, forget gate.

2 key functions are the sigmoid function \\(\sigma(x)\\)  and the \\(\tanh(x)\\)  function

 $$\sigma (x) =  \frac{\mathrm{1} }{\mathrm{1} + e^{-x} }  $$
 $$tanh (x) =  \frac{e^{2x} - 1 }{e^{2x} + 1 }  $$

 note that also \\(b\\) is any introduced bias.

#### 1. Input Gates

This gate regulates the input that goes into the LTSM unit/layer. The input gate denotes which value is to be used to modify the memory.
The sigmoid function \\(\sigma(x)\\) , decides to let through a value between \\([0,1]\\). in then creates an temporary cell \\(\tilde(C_t)\\)

{{% notice info %}}
Sigmoid and tanh, both show little difference when approach the limits, [0,1], [-1,1], and are why they a substituted out for RELU in other models.
It is one of the drawbacks of LSTM.
{{% /notice %}}

The input gate is defined by the input gate and it' relationship with the cell:

$$ i_t = \sigma(W_i\cdot[h_{t-1},x_t] + b_i) $$
$$ \tilde{C_t} = tanh(W_C\cdot[h_{t-1},x_t] + b_C) $$

#### 2. Forget Gates

The Forget gate decides which details are to be discarded from the previous block. Using a sigmoid function \\(\sigma(x)\\).
It will look at the state of the previous cell  \\(h_{t-1}\\) and the input \\(x_{t}\\) into the current cell, and outputs a number between
0 and 1, where the closer to 1, the more weight it's granted, (for each point in the vector from \\(C_{t-1}\\) ).

$$ f_t = \sigma(W_f\cdot[h_{t-1},x_t] + b_f) $$

#### Creation Of Our New Cell

Here we quickly want to highlight the building of our new cell! This needs to occur before we can produce the new \\(h_t\\). Our new Cell is built on all previous output we have generated.

- \\(f_t\\) The forgotten gate
- \\(\tilde{C_t}\\) Temporary cell (from input gate)
- \\(i_t\\) The input gate
- \\(C_{t-1}\\) The previous Cell

Which now, to give us our new  \\(C_t)\\) is

$$C_t = (f_t * C_{t-1}) + (i_t * \tilde{C_t}) $$


#### 3. Output gates

The Output gate uses the memory retrieved from the prior cell, (which contained memory from cells prior, and so on.) and input
to decide the output. The sigmoid function \\(\sigma(x)\\) determines which values to let through, and the \\(\tanh(x)\\)  function
gives weight and importance to the output, varying between \\([-1,1]\\).

$$ o_t = \sigma(W_o\cdot[h_{t-1},x_t] + b_o) $$

$$ h_t = o_t * \tanh(C_t) $$




### References

- https://www.simonspavound.com/posts/2020/09/equations-with-katex-in-hugo/
- https://aditi-mittal.medium.com/understanding-rnn-and-lstm-f7cdf6dfc14e
- https://ai.stackexchange.com/questions/18198/what-s-the-difference-between-lstm-and-rnn#:~:text=The%20main%20difference%20between%20an%20LSTM%20unit%20and,better%20the%20flow%20of%20information%20through%20the%20unit
- https://www.youtube.com/watch?v=ne-dpRdNReI
- https://colah.github.io/posts/2015-08-Understanding-LSTMs/
