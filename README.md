# NeuralHash-collision


This repository aims to answer the question of whether one can replicate AudioSeal's detector. That is, whether one can produce a classifier which admits an audio file and returns a binary as to whether the file contains AudioSeal's watermark. 

## Data
The dataset that we use is a classic one called MusicCaps found here: https://www.kaggle.com/datasets/googleai/musiccaps/data
The audio files here are scraped off of YouTube, moreover we use this repository to download the whole dataset in quick manner: https://github.com/nateraw/download-musiccaps-dataset
When downloading the audio files we decided to only download 1 second of each one with the sampling rate set to 16,000. This makes it so that the download time is quick and are files are ready in the correct form for preprocessing. 
To create a classifier that distinguishes between regular and watermarked samples we need to inject, for each file, the watermark. This will make it so that for each file in our dataset there are 2 versions,the regular and the watermarked. To generate a folder containing the watermarked versions of each file, follow Generating_WM_audio_files.ipynb
At this point we have over 10,000 audio files, each 1 second long and at a sampling rate of 16,000, where half the files contain the AudioSeal watermark and the other half don't. 
The final processing step is to convert all the audio files into 16-bit PCM, this needs to be done so that the .wav files can be succesfully decoded. To do this, just run this on your terminal
<pre> ```bash mkdir -p fixed_wavs
for f in music_data/*.wav; do
  ffmpeg -y -i "$f" -acodec pcm_s16le -ac 1 -ar 16000 "fixed_wavs/$(basename "$f")"
done
``` </pre>

## Model 







NeuralHash is Apple's perceptual hash function, which converts any image into a 96-bit hash vector. It works by, first, evaluating a neural network on the image, followed by a matrix multiplication on the output of the neural network and finally applying 0,1 thresholding on the negative and non-negative values, respectively. 

Our aim is to enigneer a collison for the NeuralHash, that is: for a given target hash vector, we want to take a given image whose hash disagrees with the target and alter it until its hash achieves the target (hence collision) such that the content of the image is still the same.

The method for how we go about achieving this task is heavily influenced by Athalye, A. (2021). NeuralHash Collider. GitHub. https://github.com/anishathalye/neural-hash-collider . 
I tried to use the code as a help as much as possible, but often times I wasn't able to reproduce the output and so I had to find a different way to get to the same destination using the code as a guide.  

A question to ask is why bother making a new repository for a problem that's already been solved. To even access NeuralHash one needs to follow the instructions in  AsuharietYgvar/AppleNeuralHash2ONNX to get the onnx version. However to do the actual work, one needs to convert it into tensorflow so as to perform the attack. The module used in neural-hash-collider wasn't successful in doing the conversion, and so I had to seek a different method for attaining the tensorflow version of NeuralHash. And so I hope that this can be thought of as an updated version for NeuralHash collision, so that folks can still find collisions by using the method found here. A total explanation of all that needs to be known (in terms of acquisation of the model, method of attack and results) can be found in the 2 notebooks.