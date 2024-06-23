---
layout: distill
title: Google Summer of Code'23
permalink: /GSoC/
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
# toc:
# #   - name: Pre-GSoC
# #     # if a section has subsections, you can add them as follows:
# #   - name: Introduction
# #       subsections:
# #       - name: Warm-Up Tasks
# #       - name: Example Child Subsection 2
#   - name: Footnotes
#   - name: Code Blocks
#   - name: Interactive Plots
#   - name: Layouts
#   - name: Other Typography?

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

<div class="row">
    <div class="col-sm mt-1 mt-md-0">
        {% include figure.html path="assets/img/combined.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


This is my Google Summer of Code 2023 project with libcamera.
<br>
This project aims to move the current V4L2 implementation of accessing and controlling cameras in OpenCV to libcamera, this will allow for better functionality for more advanced and complex cameras. This will be done by creating a videoio plugin for OpenCV to support libcamera and then creating suitable classes and functions to add the desired functionality.

## <span style="color:#FF8000"> Project Details </span>

<span style="color:#FF8000"> Contributor Name:</span> Advait Dhamorikar

<span style="color:#FF8000"> Mentors:</span> Umang Jain, Paul Elder

<span style="color:#FF8000"> Organization:</span> libcamera 

<span style="color:#FF8000"> Project:</span> Integrating libcamera into OpenCV
<hr>

<div style="padding:20px;border-style: dotted">
	<a href="/GSoC23/Pre-GSoC/"> GSoC'23 Pre-GSoC Blog </a>
</div>
<br>
<div style="padding:20px;border-style: dotted">
	<a href="/GSoC23/Final-Report/">GSoC'23 Final Report</a>
</div>
<br>
<div style="padding:20px;border-style: dotted">
	<a href="https://summerofcode.withgoogle.com/archive/2023/projects/lKttObKa">Project Page</a>
</div>