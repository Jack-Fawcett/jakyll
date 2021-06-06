---
tags: Audio Separation Convnet Tasnet convolution Fourier
categories: [Audio, Source Separation]
excerpt: "Conv-TasNet: Surpassing Ideal Time-Frequency Magnitude Masking for Speech Separation"
header:
  teaser: "/assets/Conv-TasNet%20Surpassing%20Ideal%20Time-Frequency%20Magnit%20f9828b6ef5884d7086707e03f3c44c71/conv.jpeg"
---

[Paper](https://arxiv.org/pdf/1809.07454.pdf)

Previously most speech separation approaches use time-frequency (spectrogram) features of the audio to achieve the separation which often relies on using the short-time Fourier transform (STFT). There are then two typical methods which are employed:

Generation

Using this spectrogram a model could approximate the clean (speech-separated) spectrogram of each source using nonlinear regression techniques. The clean spectrograms are used for training this kind of model.

Masking

A weighting function (mask) can be learnt for each source which can be multiplied by the original mixed audio to mask-out everything other than the source.

This method has the following drawbacks:

- The accurate reconstruction of the phase of the clean source is a non-trivial problem and sets an upper-bound on the accuracy of reconstructed audio.
- A high-resolution frequency decomposition is required for separation tasks which in turn needs a long temporal time window for the calculation of STFT. Thus increasing the minimum latency of the system. Typical window sizes:
    - Speech 32ms
    - Music > 90ms

After both methods the waveform can be recovered using the inverse short-term Fourier transform (iSTFT).

First lets familiarise ourselves with [TasNet](https://arxiv.org/pdf/1711.00541v1.pdf)

Standing for Time-Domain Audio Separation Network, in short it is an autoencoder framework which uses a 1D convolutional as the encoder and a 1D deconvolutional layer as the decoder with a LSTM network sandwiched between to estimate separation masks. 

![Conv-TasNet%20Surpassing%20Ideal%20Time-Frequency%20Magnit%20f9828b6ef5884d7086707e03f3c44c71/Untitled.png](/jakyll/assets/Conv-TasNet%20Surpassing%20Ideal%20Time-Frequency%20Magnit%20f9828b6ef5884d7086707e03f3c44c71/Untitled.png)

Since the network directly works with the waveform it can have a very low latency (5ms).

Although TasNet outperformed previous time-frequency speech separation methods, it relies on an LSTM which for longer audio or smaller time windows quickly becomes unmanageable to train as well as having a large computational cost. 

Thus Conv-TasNet, a fully-convolutional network. Just as TasNet the flow goes as follows:

1. Encode
2. Separate
3. Decode

![Conv-TasNet%20Surpassing%20Ideal%20Time-Frequency%20Magnit%20f9828b6ef5884d7086707e03f3c44c71/Untitled%201.png](/jakyll/assets/Conv-TasNet%20Surpassing%20Ideal%20Time-Frequency%20Magnit%20f9828b6ef5884d7086707e03f3c44c71/Untitled%201.png)

The difference lies in only using convolutional layers, even in the separation module.

First the input sound is divided into segments which is then fed through a 1D convolution layer which outputs the encoded waveform. The decoder does this but in reverse to go from audio embedding to audio.

The separation is done by learning a number (number of speakers) of masks which have the constraint that each weight can only be 1 or 0, hence removing select embeddings from the mixture. The sum of all mask weights will add up to 1 and thus return the unaltered audio.  

The separator modules are made up of stacked 1D dilated convolutional blocks.

![Conv-TasNet%20Surpassing%20Ideal%20Time-Frequency%20Magnit%20f9828b6ef5884d7086707e03f3c44c71/Untitled%202.png](/jakyll/assets/Conv-TasNet%20Surpassing%20Ideal%20Time-Frequency%20Magnit%20f9828b6ef5884d7086707e03f3c44c71/Untitled%202.png)

The difference between dilated and non-dilated convolutional blocks can most easily be explained by the following picture of 2D dilated convolutions, where when the dilation factor is 1, it is the equivalent to a normal convolution.

![Conv-TasNet%20Surpassing%20Ideal%20Time-Frequency%20Magnit%20f9828b6ef5884d7086707e03f3c44c71/Untitled%203.png](/jakyll/assets/Conv-TasNet%20Surpassing%20Ideal%20Time-Frequency%20Magnit%20f9828b6ef5884d7086707e03f3c44c71/Untitled%203.png)

It is then easy to understand how given a 1D dilated convolution could potentially replace a LSTM; since both rely on the temporal context; By stacking increasingly dilated convolution layers to cover a sufficiently large time window for speech signal analysis.

1x1 Convolutional layers (point-wise convolution) are used to reduce the features to finally estimate the mask.

You may notice two paths from the convolutional block; a skip-connection path and a residual path. The residual path is used as the input to the next block, hence sharing on information from the current mask. The skip-connection path is used as the output for all the blocks.

![Conv-TasNet%20Surpassing%20Ideal%20Time-Frequency%20Magnit%20f9828b6ef5884d7086707e03f3c44c71/Untitled%204.png](/jakyll/assets/Conv-TasNet%20Surpassing%20Ideal%20Time-Frequency%20Magnit%20f9828b6ef5884d7086707e03f3c44c71/Untitled%204.png)