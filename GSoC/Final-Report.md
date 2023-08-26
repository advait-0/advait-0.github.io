---
layout: distill
title: Google Summer of Code'23
permalink: /GSoC23/Final-Report/
description: Final blog of my project integrating libcamera into OpenCV
giscus_comments: false
date: 2023-08-25

authors:
  - name: Advait Dhamorikar
    # url: "https://en.wikipedia.org/wiki/Albert_Einstein"
    affiliations:
      name: Google Summer of Code
  # - name: Boris Podolsky
  #   url: "https://en.wikipedia.org/wiki/Boris_Podolsky"
  #   affiliations:
  #     name: IAS, Princeton
  # - name: Nathan Rosen
  #   url: "https://en.wikipedia.org/wiki/Nathan_Rosen"
  #   affiliations:
  #     name: IAS, Princeton

# bibliography: 2018-12-22-distill.bib

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
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


# Below is an example of injecting additional post-specific styles.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }

---
This is my Google Summer of Code 2023 project with libcamera.

It aims to move the current V4L2 implementation of accessing and controlling cameras in OpenCV to libcamera, this will allow for better functionality for more advanced and complex cameras. This will be done by creating a videoio plugin for OpenCV to support libcamera and then creating suitable classes and functions to add the desired functionality.

## <span> Project Details </span>

<span style="color:#B509AC"> Contributor Name:</span> Advait Dhamorikar

<span style="color:#B509AC"> Mentors:</span> Umang Jain, Paul Elder

<span style="color:#B509AC"> Organization:</span> libcamera 

<span style="color:#B509AC"> Project:</span> Integrating libcamera into OpenCV

<span style="color:#B509AC"> Links:</span>
* [Code](To be added)
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
(add link)

<br>


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

