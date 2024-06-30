---
layout: post
permalink: /GSoC24/GSoC-Update1/
title: What is my project about?
description: AAC Passthrough?
date: 2024-06-29 20:43:16
# nav: true
# nav_order: 3
---

My project "Adding AAC via S/PDIF and Bluetooth" is one that really allows me to learn a lot about multimedia development nd allows me to apply the knowledge i've learnt in college at the some time.
I'll try to explain some aspects and updates about my work through this blog.

## AAC
AAC is the Advanced Audio Codec developed for lossy digital audio compression. AAC has been standardized by ISO and IEC as part of the MPEG-2 and MPEG-4 specifications. AAC encoded content is often used in MPEG-4 .mp4 and .mp4a(audio only containers).
Containers can be considered as storage mediums for multiple data streams such as video, audio and subtitle files. These containers such as .mkv (developed by one of the mentors at VLC!) then has to have it's data demuxed and then decoded.
AAC uses a modified direct cosine transform (DCT) algorithm for encoding the analog signal.

## What is Passthrough?
When a signal is allowed to move from the source to the destination without altering its form it is known as passthrough.
When using HDMI(High Definition Media Interface with S/PDIF frames) or Bluetooth for transmission the input signal is often altered.
Without this passthrough it may happen that you may get a lesser fidelity sound.
It might be seen with 3D playback as supported HDMI's might allow 3D playback with surround sound while unsupported versions might block 3D playback.
For the initial stage of the project I am working on the passthrough for Android using the Audiotrack API.

### What is currently happening?
In our case considering VLC player, VLC doesn't currently support AAC passthrough.

Consider an AAC encoded 2 channel(stereo sound setup) sampled at 44.1KHz
Whenever an AAC encoded stream is played from a container say .aac or .mp4a then firstly the demux is called which then separates the audio stream and looks for a subtitle stream for music lyrics.

These samples are then packetized by a packetizer.
Some containers (like AVI) cannot distinguish between raw samples and those with headers. Therefore, they forward these samples to a packetizer. The packetizer's role here is to detect the presence of these headers and properly handle or strip them, preparing the stream for the next stage (e.g., decoding or remuxing).

These packets are then passed to the decoder, the decoding is handled by ffmpeg's decoding algorithms which in our case is the Lavc59.37.100.
It then is pre-buffered for playback.

There are currently 3 different methods for audio output on Android:
1. OpenSLES - not used by default
2. AudioTrack - legacy and supports passthrough
3. AAudio - newer faster and in development

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IEC 61937 Frame Structure</title>
    <style>
        figure {
            text-align: center;
        }
        img {
            width: 70%; /* Adjust the width as needed */
            height: auto; /* Maintain aspect ratio */
        }
    </style>
</head>
<body>
    <figure>
        <img src="/assets/img/decode_log.png">
        <figcaption>Initial AAC playback log.</figcaption>
    </figure>
</body>
</html>

We are working on the AudioTrack API for this project.

### What am I working on?
I am working to avoid decoding the signal and sending a PCM output to amplifiers instead of the original AAC.

Here are a few terminologies to help you understand better:

**Sample**: The sampling rate determines the number of samples taken per second. For a sampling rate of 44.1KHz we take 44100 samples every second. According to the Nyquist theorem, to accurately reproduce a signal, the sampling rate must be at least twice the highest frequency present in the signal. For human hearing, the maximum frequency is about 20 kHz, so 44.1 kHz is more than sufficient for audio applications.

**Channel**: An audio channel is a single stream of audio information. Each channel can carry a separate sound signal, and when multiple channels are combined, they create a spatial audio experience for the listener.

The most common configurations are mono audio(single channel), stereo 2.0, 5.1 and 7.1.

The first digit corresponds to the full bandwidth channels(left, center, right, left surround, right surround) and the digit after the decimal signifies the low-frequency channel(subwoofer) for bass.

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=0.8">
    <title>IEC 61937 Frame Structure</title>
    <style>
        figure {
            text-align: center;
        }
        img {
            width: 60%; /* Adjust the width as needed */
            height: auto; /* Maintain aspect ratio */
        }
    </style>
</head>
<body>
    <figure>
        <img src="https://help.apple.com/assets/6578E9988C0AF588E10B7A50/6578E9992A748263270FFD12/en_US/c1f378805b3a8857af32980da150de0b.png" alt="General Channel Configurations" style="width: 50%; height: auto;">
    </figure>
</body>
</html>

**Frame**: A frame is defined as the number of samples * the number of channels. 

Let us consider the quantization bit depth of one sample as 16 bit, hence 1 sample occupies 2 bytes. Therefore for a stereo setup with 1 sample each of 2 channels.
As per our frame definition it will hold 2 samples, therefore our **bytes per frame** comes out to be 4.

**Buffer**: The native method typically uses a target buffer duration to ensure smooth playback, this buffer queues the data in memory. This duration can vary but is often around 100 to 200 milliseconds. This duration ensures that there is enough buffered audio data to handle fluctuations in data delivery without causing underflows.

**Buffer size** = Frame size * (Sample rate * Target duration)

The general workflow begins by reading the audio file and opening the ES(Elementary stream) handler which initializes the demux structures.

It then calls the S/PDIF pass-through decoder, which acts like a fake codec just to forward the encoded data.
Support for MP4A was added to this\
`case VLC_CODEC_MP4A`

It then sets the `decoder_t *p_dec` properties like the codec, profile and the format.


<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>log</title>
    <style>
        figure {
            text-align: center;
        }
        img {
            width: 100%; /* Adjust the width as needed */
            height: auto; /* Maintain aspect ratio */
        }
    </style>
</head>
<body>
    <figure>
        <img src="/assets/img/Audiotrack_logcat2.jpeg">
        <figcaption>Initial AAC playback log.</figcaption>
    </figure>
</body>
</html>

An audio output stream is then started and then Start from device.c is executed. This function is responsible for choosing the best API for aout. As discussed above it chooses from AAudio, AudioTrack and OpenSLES. For our passthrough cases and scenario it chooses the AudioTrack API initially by using a function pointer.

As seen in our format->i_format in the logs we can see that the detected format 1630826605 corresponds to FourCC for mp4a.
FourCC(Four Character Code) is a sequence of four bytes used to uniquely identify data formats, such as video or audio codecs, in multimedia files. It is commonly used in container formats like AVI, MP4, and QuickTime to specify the codec used for encoding the media content.

audiotrack.c is then called which where the `b_try_passthrough` bool flag is set if the condition for the Android Audio Device type is met as Encoded.

The `i_bytes_per_frame` defined in the struct `audio_format_t` as discussed above defined for compressed frame are extracted to be 364 which is unreasonably high and one of the culprits for our failing passthrough.

We are testing with AAC encode 2 channel stereo sound at 44.1KHz.
If passthrough is good to go StartPassthroughFmt calls the GetPassthroughFmt for configuring the format, channels and other parameters. We then configure and create Android AudioTrack using the Java Native Interface(JNI)

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>log</title>
    <style>
        figure {
            text-align: center;
        }
        img {
            width: 100%; /* Adjust the width as needed */
            height: auto; /* Maintain aspect ratio */
        }
    </style>
</head>
<body>
    <figure>
        <img src="/assets/img/Audiotrack_logcat.jpeg">
        <figcaption>Initial AAC playback log.</figcaption>
    </figure>
</body>
</html>

Android AOSP's internal function then returns the minimum buffer size for our configuration which is calculated using the method given above adding some additional buffer margin.

However post this I am encountering an error where Java throws an exception `Invalid audio buffer size`.

Investigating this in the Android codebase one can see
```java
 private void audioBuffSizeCheck(int audioBufferSize) {
        // NB: this section is only valid with PCM or IEC61937 data.
        //     To update when supporting compressed formats
        int frameSizeInBytes;
        if (AudioFormat.isEncodingLinearFrames(mAudioFormat)) {
            frameSizeInBytes = mChannelCount * AudioFormat.getBytesPerSample(mAudioFormat);
        } else {
            frameSizeInBytes = 1;
        }
        if ((audioBufferSize % frameSizeInBytes != 0) || (audioBufferSize < 1)) {
            throw new IllegalArgumentException("Invalid audio buffer size.");
        }
        mNativeBufferSizeInBytes = audioBufferSize;
        mNativeBufferSizeInFrames = audioBufferSize / frameSizeInBytes;
    }
```

We can see that it is because the modulo division is failing as the given buffer size of 35440 isn't divisible by our frame size in bytes.
This is the issue I'm currently working on and I hope the Android AAC passthrough will be enabled really soon!

Thanks for reading my update blog!
I'll be back soon.