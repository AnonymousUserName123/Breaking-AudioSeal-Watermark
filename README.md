# NeuralHash-collision


This repository aims to answer the question of whether one can replicate AudioSeal's detector. That is, whether one can produce a classifier which admits an audio file and returns a binary as to whether the file contains AudioSeal's watermark. 

## Data
The dataset that we use is a classic one called MusicCaps found here: https://www.kaggle.com/datasets/googleai/musiccaps/data

The audio files here are scraped off of YouTube, moreover we use this repository to download the whole dataset in quick manner: https://github.com/nateraw/download-musiccaps-dataset

When downloading the audio files we decided to only download 1 second of each one with the sampling rate set to 16,000. This makes it so that the download time is quick and are files are ready in the correct form for preprocessing. 
To create a classifier that distinguishes between regular and watermarked samples we need to inject, for each file, the watermark. This will make it so that for each file in our dataset there are 2 versions,the regular and the watermarked. To generate a folder containing the watermarked versions of each file, follow Generating_WM_audio_files.ipynb
At this point we have over 10,000 audio files, each 1 second long and at a sampling rate of 16,000, where half the files contain the AudioSeal watermark and the other half don't. 
The final processing step is to convert all the audio files into 16-bit PCM, this needs to be done so that the .wav files can be succesfully decoded. To do this, just run this on your terminal:
<pre> 
mkdir -p fixed_wavs
for f in music_data/*.wav; do
  ffmpeg -y -i "$f" -acodec pcm_s16le -ac 1 -ar 16000 "fixed_wavs/$(basename "$f")"
done
</pre>

## Model 

We want to fit a convolutional neural network on tensorflow. A previous project I worked on also dealt with working on audio files, but there it was to classify songs in terms of 4 genres. I figured that given our task here is similarly an audio classification problem, it would be sensible to use the same architecture here as the one used there.

## Results

Unfortuently, while CNNs were able to classify audio files by genre, they seem to be unfit at achieving the task of differentiating between audio samples and the original samples (this can be observed on NN_mimic_final.ipynb) 
