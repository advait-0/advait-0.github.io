---
layout: post
permalink: /GSoC24/GSoC-Begins/
title: Another GSoC?
description: Why I'm doing GSoC
date: 2024-06-23 10:40:16
# nav: true
# nav_order: 3
---

## Pre-GSoC
After my last GSoC, I was really inclined to learn more about multimedia development. So, I started looking for opportunities in the industry. However, lady luck had other plans. Despite interviewing with some tech giants, I did not secure an internship.

Given the situation, my best option was to participate in the Google Summer of Code again. I didn't want to spend my summer doing a job I didn't like, one that would offer an inferior learning experience. I started looking for organizations that matched my interests, and two of them were VideoLAN and OpenCV.

Fast forward two months, and lady luck finally smiled upon me. I was accepted as a contributor by both VideoLAN and OpenCV and had to choose which organization I wanted to work with. After giving it a lot of thought, I chose to work with VideoLAN for a new challenge and to learn something new.

### So what is VideoLAN and my Project about?
<a href="https://www.videolan.org/">VideoLAN</a> is a not-for-profit organization that develops and maintains a lot of open source projects which are used by billions worldwide including VLC Player, libVLC, x264, x265 and more..\
<a href=" https://summerofcode.withgoogle.com/myprojects/details/eyxxsgo0">My Project</a> involves adding AAC via S/PDIF and Bluetooth support for direct passthrough.


## Project Idea
VLC is an extensive open-source media player with a solid backend capable of handling anything you throw at it. It's libVLC core is modularized into hundreds of plugins, which may be loaded at runtime. This architecture provides great flexibility to developers to create a wide range of multimedia applications using the VLC features.

VLC currently can't handle direct passthrough of AAC encoded streams instead it decodes it and sends a PCM stream to be played by the amplifier, this needs to be fixed for all major supported OS platforms.
This project will certainly put to good use multiple courses i've learnt in college including DSP, Communications, MPMC and more..

## Warm-Up Tasks

```
Submit your idea

You need to submit your idea on the Google Summer of Code platform.

You should do so, very quickly, even before having finished compilation and the next steps, so that we can give you early feedback.
Compile VLC or libVLC

This may sound trivial, but it's harder than many expect. You must compile the project you want to work on.

See https://wiki.videolan.org/Category:Building/ for more informations.

You should come on IRC to get help to compile. 
```

For the new open-source contributors who want to contribute in this project and aren't familiar with its codebase some warm-up tasks were provided to get an understanding of how VLC works under the hood.

## Building VLC-desktop and VLC-Android
Building VLC desktop is a pretty straigtforward process as long as you have the right dependencies for your system. You can always use docker or just the docker dependencies to build locally.

Here's a short guide for building on VLC-4 Android on Ubuntu/Debian:

### 1. Install the dependencies
```
sudo apt-get update && \
     apt-get -y dist-upgrade && \
     apt-get install --no-install-suggests --no-install-recommends -y \
        openjdk-17-jdk-headless ca-certificates autoconf m4 automake ant autopoint bison \
        flex build-essential libtool libtool-bin patch pkg-config cmake meson \
        git yasm g++ gettext ninja-build \
        wget expect unzip python3 python3-venv python3-setuptools python3-mako \
        locales libltdl-dev curl nasm gperf \
        protobuf-compiler && \
```

### 2. <a href="https://developer.android.com/sdk/index.html">Download</a>  and add the correct Sdk and Ndk to your path
(as of now the latest Sdk and Ndk r25c should work)

`export ANDROID_SDK=/path/to/android-sdk`\
`export ANDROID_NDK=/path/to/android-ndk`
`export PATH=$PATH:$ANDROID_SDK/platform-tools:$ANDROID_SDK/tools`

### 3. Clone the repo
`git clone https://code.videolan.org/videolan/vlc-android.git`

### 4. Compile and build
To build VLC-4 for arm64 you can use the following command.
You can also use Android Studio for further development.
`./buildsystem/compile.sh -a arm64 -vlc4`

Generated apk's can be found in vlc-android/build/outputs/apk.


## Understanding what needs to be done
So what currently is happening is that when you play an aac encoded audio file on VLC player instead of directly getting passed through for your audio device to handle it gets decoded into a PCM stream.

HDMI and S/PDIF pass encoded data in a IEC61937 standard frame.

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
        <img src="/assets/img/IEC.png">
        <figcaption>IEC 61937 Frame.</figcaption>
    </figure>
</body>
</html>



VLC core has a toSPDIF converter that has to be modified to support AAC payload.





Whereas for bluetooth it gets encapsulated in an A2DP frame.

Both of these structures deserve a separate blog for explanation and I will put out a detailed blog for the AAC codec and these structures soon.

### Closing Note
I'm very excited to contribute to my all time favourite media player and learn from master mentors.
I'll keep this blog updated with all the new stuff I learn throughout my journey.
Thanks for reading this blog!

 

