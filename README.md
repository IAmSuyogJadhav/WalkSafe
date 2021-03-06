
## Idea Description
We are developing an application that will alert unaware pedestrians and those with
hearing disabilities if a car is approaching them. Has applications ranging from
helping reduce the no. of accidents occuring due to pedestrians carelessly crossing the streets
with earphones on, alerting players of VR/AR games (for e.g., Pokemon Go) of oncoming
cars to allowing people with aural impairments cross the streets with ease. We call it **WalkSafe**.

> WalkSafe lets you walk freely on streets and everywhere else in general, even when you are wearing earphones or have difficulties in hearing.

## Dataset Preparation
> Refer to [`Dataset Preparation.ipynb`](./Dataset%20Preparation.ipynb) to jump to the code directly.

We are using an open dataset (titled [Urban8K](https://urbansounddataset.weebly.com/urbansound8k.html)) containing various
urban sounds including car horns. We also use about an hour long recording of
urban background noise to generate unique positive examples for training our model.
We cropped out individual horns from the dataset and randomly superposed them
on 2 second clippings from the background noise to create 1800 positive and 700 negative
training examples. The figure given below shows how the individual bursts of horn were cropped. The cropped out horns were about 0.4 seconds long and were randomly superposed over 2 seconds long clips of background noise.

|             Original              |          After cropping          |
| :-------------------------------: | :------------------------------: |
| ![original](./media/original.png) | ![original](./media/cropped.png) |



The dataset was then labeled by creating an array having 10 `1`s starting right
from the point where the horn clip _just ends_ and zeros elsewhere.
This method usually gives good results since we term a sound to be horn only _after_
it has ended. This perfectly aligns with the fact that one cannot predict a sound to be horn by only listening to a really miniscule sample of it (1 ms in this case!)
The labels were generated along with the training examples and were saved in a Numpy pickle.

## Audio pre-processing
> Refer to [`graph_spectrogram` function in `utils.py`](./utils.py#L7) to jump to the code directly.

Audio data, if used raw, doesn't come with a good set of usable features. One has to
pre-process the raw audio signal to make it suitable for training purpose. All the modern microphones use 44.1 KHz sampling rate to record the audio. If we load the raw audio (which is 2 seconds long) directly, we therefore get a vector of shape  <img src="https://latex.codecogs.com/gif.latex?[882000%20*%201]" />. This is a whooping lot of features!

We can reduce the no. of features drastically by switching to a more suitable domain, i.e., frequency domain. There are a large no. of frequencies in a typical audio stream. The figure given below shows how one is likely to have a lot of features in the time domain whereas, in frequency domain, the number of them gets significantly reduced due to the discrete nature of frequencies. A signal in time domain can be converted to frequency domain by calculating [Short-time Fourier Transform](https://en.wikipedia.org/wiki/Short-time_Fourier_transform) over the audio signal.

| ![Time Domain Vs Frequency Domain](./media/time_vs_frequency_domain.jpg) |
| :----------------------------------------------------------: |
|               Time Domain Vs Frequency Domain                |



We chose a _window size_ of 1650 time steps, _step size_ of 65 time steps and _fft length_ to be 200 after multiple trials.
(More details about these parameters are given in the docstring of [`graph_spectrogram`](./utils.py#L7) function inside the [`utils`](./utils.py) script)

This provides us back with a matrix of shape <img src="https://latex.codecogs.com/gif.latex?[1332%20*%20101]" />.
This is a lot of reduction in the parameters, plus frequency domain is the most suitable domain
for dealing with signals and always gives better results than the time domain. Thus,
finally we end up with a highly useful, yet computationally efficient set of parameters.

## Training the model
> Refer to [`Training_model.ipynb`](./Training_model.ipynb) to jump to the code directly.
The code used to train the model is contained in the above file.

> This documentation is slightly incomplete and will be completed in due time.

## The Team

| ![Aniket](./media/Aniket.jpg) | ![Suyog](./media/Suyog.jpg) | ![Udbhav](./media/Udbhav.jpg) |
| :---------------------------: | :-------------------------: | :---------------------------: |
|       **Aniket Teotia**       |      **Suyog Jadhav**       |       **Udbhav Bamba**        |
