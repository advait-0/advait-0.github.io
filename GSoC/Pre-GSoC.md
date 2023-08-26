---
layout: post
permalink: /GSoC23/Pre-GSoC/
title: Road to Google Summer of Code
description: Here is a blog documenting my GSoC timeline.
date: 2023-04-02 16:40:16
# nav: true
# nav_order: 3
---

## GSoC Prep
I've always been interested in Linux development ever since I started using Linux back in 2019 and have kept distro hopping ever since,
honestly because my laptop couldn't handle Windows back then.
I thought about applying for GSoC to get to learn about open source development and started looking for organizations.
I scrolled through a plethora of projects and orgs and then came across the projects at libcamera.
The projects integrating libcamera into OpenCV along with adding UVC timestamp support piqued my interest particularly because
I wanted to explore some part of the working of the Linux kernel, also having explored OpenCV fairly while competing in the <a href="https://www.portal.eyantra.com.com">eYantra Robotics Competition</a>
by <a href="https://www.iitb.ac.in">IIT Mumbai (Bombay)</a> it aligned perfectly with my interests.

### So what is libcamera?
libcamera is an open source camera stack for many platforms with a core userspace library, built to extend the functionality of Linux modules like V4L2 that aren't suitable for modern hardware. It aims to control the complexity of embedded camera hardware by providing an intuitive API allowing for open source solutions as well as supporting vendor IP.

## Project Idea
Integrating libcamera into <a href="https://opencv.org">OpenCV</a>

OpenCV is an extensive open-source Computer-Vision library providing a plethora of tools for image processing and analyzing and computer camera data. Images can be easy mainpulated with it's built-in algorithms.

OpenCV currently relies primarily using V4L2 on Linux as it's backend for capturing it's frames, the plan in a nutshell is to shift all this functionality to libcamera considering it's advantages.

## Warm-Up Tasks

    Build libcamera, as well as the application of your choice
    Study the libcamera interface. Building a test application, or reading both simple-cam and the libcamera documentation can help.
    Study how the application of your choice interfaces with its video devices. If documentation is available, study that. If not, study how the application interfaces with other video devices, such as V4L2, which is what is usually currently used for Linux systems.
    Design how you would connect the application’s video interface with libcamera. This will be the roadmap of the implementation.

For the new open-source contributors who want to contribute in this project and aren't familiar with its codebase some warm-up tasks were provided to get an understanding of how libcamera works under the hood.

### 1. Building Libcamera
I set-off by cloning the latest git of libcamera, installing all the required Debian dependencies as well as the additional one's required for cam mentioned in the <a href="https://libcamera.org/getting-started.html#">getting started</a> guide .
I had previously used the Make and CMake build systems, this was my first time using meson. 
I then built and installed it successfully.

To test the install I tried to take a picture:
1. First off I ran  `cam -h` to understand what its various functionality and supported commands are.
2. Next I checked my available system cameras, using `cam -l` to get the index of my available cameras.
3. Using `cam -c1 -C1 -Fimage` I captured and stored my image
The flag -c is used for selecting the camera by index, -C is used to capture the frame and -F is used to write and store it on your disk.

### 2. Building OpenCV
To get the latest developmental build of OpenCV it was essential to install from source it was essential to fulfill its dependencies. Some of which were for its builds, gtk, some for supporting various image formats.
(These steps are for a Debian based Linux distro)

```
    sudo apt install build-essential cmake git pkg-config libgtk-3-dev \
    libavcodec-dev libavformat-dev libswscale-dev libv4l-dev \
    libxvidcore-dev libx264-dev libjpeg-dev libpng-dev libtiff-dev \
    gfortran openexr libatlas-base-dev python3-dev python3-numpy \
    libtbb2 libtbb-dev libdc1394-22-dev libopenexr-dev \
    libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev
```
Create a new folder for its install
```
mkdir ~/opencv_build && cd ~/opencv_build
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git
```
4.7.0 is the latest OpenCV version at the time of writing this.
```
cd ~/opencv_build/opencv
mkdir -p build && cd build
```
We shall use CMake to configure our build

```
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D INSTALL_C_EXAMPLES=ON \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D OPENCV_GENERATE_PKGCONFIG=ON \
    -D OPENCV_EXTRA_MODULES_PATH=~/opencv_build/opencv_contrib/modules \
    -D BUILD_EXAMPLES=ON ..
```
The flags are particularly important
especially the `PKGCONFIG` flag for me as without it the system wouldn't find the OpenCV install for me on another project.

Now to compile
```
make -j4
```
`-j` is the flag for specifying the number of CPU theads for faster building

then
``` 
sudo make install
```
to install OpenCV.


### 3. Understanding the Workings
To get an understanding of how libcamera interacts with its camera devices to capture frames, I studied the <a href="https://git.libcamera.org/libcamera/simple-cam.git/tree/simple-cam.cpp">simple-cam</a> codebase.
 What simple-cam does to capture frames is that it first requests for the default camera handle from the user and then acquires the camera for its use, then perform the subsequent operations for managing the stream, configuring the camera ```configure(config.get());```, allocating buffers ```allocator->buffers(cfg.stream()).size();```. We can then finally capture the frames by starting the camera ```start()```, running an event loop, stopping the camera ```stop()```, freeing the buffer and then finally releasing the camera.

### 4. How OpenCV currently captures frames
 
 OpenCV currently supports the backends Microsoft Media Foundation(MSMF), Video for Linux 2(V4L2) out of the box, 3rd party backend like libcamera will have to be enabled like plugins at runtime, this is how even gstreamer can be used with OpenCV at the moment. The VideoCapture class in this module accesses cameras using V4L2 MMAP method to access the camera data. It then makes a local copy of the image data using memcpy to provide it in the OpenCV Mat format.Alternately, the use of V4L2 API's was preferred as it saved computing and improved efficiency by avoiding the memcpy() process altogether. V4L2 is the default backend for Linux systems as of now, a sample representation can be found <a href="https://jayrambhia.com/blog/capture-v4l2">here</a>.

### 5. The roadmap for the project
- I had a pretty clear idea for the project after reading through a lot of documentation and consulting the friendly irc for help.
- The plan is to link libcamera to OpenCV to allow it to be used as a backend, So I’ll build a  <a href="https://docs.opencv.org/4.x/d0/d3d/tutorial_general_install.html">plugin</a> that will be stored in opencv/modules/videoio/misc that can be called while building OpenCV to be used with libcamera.
- Then creating a suitable class cv::CAP_libcamera that handles the backend when cv::VideoCapture() is being used 
- The plugin shall request for the default camera handle from the user then acquire the camera, then perform the subsequent operations for managing the stream, configuring the camera ```configure(config.get());```, allocating buffers ```allocator->buffers(cfg.stream()).size();```. We can then finally capture the frames by starting the camera ```start()```, running an event loop, stopping the camera ```stop()```, freeing the buffer and then finally releasing the camera as demonstrated by simple-cam.
- After it has been established that cv::VideoCapture() works with libcamera as its backend, I plan to work on adding additional configuration options like managing frame properties by adding additional flags for functionality supported by libcamera that might be necessary as per the community and the mentors as described <a href="https://docs.opencv.org/4.x/dc/dfc/group__videoio__flags__others.html">here</a>.

### Contributions
While understanding and going through the documentation and codebase I found some fix and submitted this <a href="https://git.libcamera.org/libcamera/libcamera.git/commit/?id=cee03cd183126d1e0828c8ae2cc8706de2404b73">patch</a> as my contribution


### Closing Note
I'm very excited to contribute to open source and to libcamera during my summer break it will be fun and a great learning experience.
I'll keep updating this with my experience if I get selected to contribute.
Thanks for reading!

 

