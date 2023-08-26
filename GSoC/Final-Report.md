---
layout: distill
title: Google Summer of Code'23
permalink: /GSoC23/Final-Report/
description: Final blog of my project integrating libcamera into OpenCV
giscus_comments: false
date: 2023-08-25

authors:
  - name: Advait Dhamorikar
    # url: "https://en.wikipedia.org"
    affiliations:
      name: Google Summer of Code
 


toc:
  # - name: Pre-GSoC
  #   # if a section has subsections, you can add them as follows:
  - name: Project Details
  #     subsections:
  #     - name: Warm-Up Tasks
  #     - name: Example Child Subsection 2
  - name: Workflow Overview
    subsections:
        - name: Adding libcamera support
        - name: OpenCV backend

  - name: Future Work
  - name: Acknowledgements

---
This is my Google Summer of Code 2023 project with libcamera.

It aims to move the current V4L2 implementation of accessing and controlling cameras in OpenCV to libcamera, this will allow for better functionality for more advanced and complex cameras. This will be done by creating a videoio plugin for OpenCV to support libcamera and then creating suitable classes and functions to add the desired functionality.

## <span> Project Details </span>

<span style="color:#B509AC"> Contributor Name:</span> Advait Dhamorikar

<span style="color:#B509AC"> Mentors:</span> Umang Jain, Paul Elder

<span style="color:#B509AC"> Organization:</span> libcamera 

<span style="color:#B509AC"> Project:</span> Integrating libcamera into OpenCV

<span style="color:#B509AC"> Links:</span>
* [Github](https://github.com/advait-0/opencv/tree/libcamera-final)
* [Project Page](https://summerofcode.withgoogle.com/archive/2023/projects/lKttObKa)  


<br>

## Workflow Overview
OpenCV is a popular computer-vision library used that offers ease of usage,
libcamera is a camera-stack that can handle the complexity of modern embedded cameras with support for open and closed source IPAs.
The project aimed to integrate libcamera into the OpenCV backend.

### Adding libcamera support
If you have libcamera installed<d-footnote>https://libcamera.org/getting-started.html</d-footnote> and use the libcamera backend when creating a VideoCapture object using
{% highlight javascript %}
cv::VideoCapture cap(0,cv::CAP_LIBCAMERA)
{% endhighlight %}
and if you had used this flag when using CMake
{% highlight javascript %}
-D WITH_LIBCAMERA=ON
{% endhighlight %}
macros have been setup which check if your installation is working, thus the {% highlight javascript %}HAVE_LIBCAMERA{% endhighlight %} flag will be set to true.

### OpenCV backend
The call to libcamera is made primarily by the `cap_libcamera.cpp` which handles all of the function calls,
Where the Class `CvCapture_libcamera_proxy` is derived from OpenCVs `IVideoCapture` Class its member functions include:
- Constructor: This is where the Camera Manager thread is started and `cam_init` is called which is responsible for error handling and then finally opening the camera.
- `open(int index)`: The camera of the specified index gets acquired here, a configuration for it is generated based on a chosen streamrole which include ViewFinder, VideoRecording, Raw or StillCapture. We can make additional changes to the configuration here like choosing the desired pixelformat(YUYV, MJPEG, NV12 etc) and setting the dimensions of the stream. The configuration if supported is then validated and buffer allocation is done.
Created requests are queued to camera.
- `grabFrame()`: Used to get the frames from the camera.
- `retrieveFrame()`: Processes the frames retrieved from the queue one by one calls the convertToRgb() functions which converts the planar data of various stream format into supported BGR/RGB, queuing the buffers for reuse again.
    - `convertToRgb()`: This function checks the streamconfig for the  format and finds its buffers. It then `mmap`s those buffers data and returns a file descriptor.
    The mapped data is then processed according to it's pixelformat and returned to a `cv::Mat container`.

<br>

## Contributions
You can check my commits and code here:
1. [Adding necessary libcamera addons to backend](https://github.com/opencv/opencv/commit/bfef36cec41571c62f383d92ee5fbc6a47673e24) 
2. [Implement open functionality](https://github.com/opencv/opencv/commit/e8f2cc51aaa45ea4eb0e23273a3cc8b882ce84a2)
3. [Add buffer allocation capabilities](https://github.com/opencv/opencv/commit/3bbbfff7d9487c99e2394ed97dc13a3356ee024e)
4. [Add all necessary IVideoCapture functions framework](https://github.com/opencv/opencv/commit/796aaa2995d917cfedc3bf40201862303425bafd)
5. [Add pixelformat and colour conversion capabilities](https://github.com/opencv/opencv/commit/ee070868e67a5d6c5348854527c401794e6eefb4)
6. [Bug fixes and optimizations](https://github.com/opencv/opencv/commit/c7759e45e7cbff8e4654f0f3ed62ad93af95371f)

<br>

### Known issues
The backend has been integrated and supports majority of the IVideoCapture functions and the stream data is successfully being mmaped, however the format and colour conversion of this mapped data is still in testing for YUYV at the time of writing this blog.

## Future Work
- [ ] &nbsp;Add support for additional formats like NV12.
- [ ] &nbsp;Utilize dma to have a zero copy workflow.

<br>

## Acknowledgements
A very special thanks to my mentors Umang Jain(uajain), Paul Elder (epoll) for always answering my doubts and supporting me throughout.
It was a great experience working with them and getting to learn so many new things as a part of the Google Summer of Code.
I would also like to extend thanks to Laurent Pinchart(pinchartl), Kieran Bingham(kbingham) and other members of the libcamera organization who helped with my queries.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/libcamera.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-3 mt-3 mt-md-0">
        {% include figure.html path="assets/img/gsoc.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    libcamera + GSoC = :-)
</div>

