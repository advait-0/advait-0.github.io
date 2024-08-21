---
layout: distill
title: Google Summer of Code'24
permalink: /GSoC24/
description: Final blog of adding AAC via S/PDIF and Bluetooth support
giscus_comments: false
date: 2024-08-22

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

<div class="row justify-content-sm-center">
    <div class="col-sm-4 mt-3 mt-md-0 text-center">
        {% include figure.html path="assets/img/VLC_icon.png" title="VLC Icon" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0 text-center">
        {% include figure.html path="assets/img/gsoc.png" title="GSoC Logo" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption text-center">
    Google Summer of Code '24
</div>


This is my Google Summer of Code 2024 project with VideoLAN.
<br>
This project aims to add passthrough for the AAC codec for hardware decoding over S/PDIF and Bluetooth for
enchanced audio as quality is often lost due to the decoding and re-encoding due to the lossy nature of the codec.
## <span style="color:#FF8000"> Project Details </span>

<span style="color:#FF8000"> Contributor Name:</span> Advait Dhamorikar

<span style="color:#FF8000"> Mentors:</span> Thomas Guillem

<span style="color:#FF8000"> Organization:</span> VideoLAN

<span style="color:#FF8000"> Project:</span>  Add AAC via S/PDIF and Bluetooth support
<hr>

<div style="padding:20px;border-style: dotted">
	<a href="/GSoC24/Final-Report/"> GSoC'24 Final Report </a>
</div>
<br>
<div style="padding:20px;border-style: dotted">
	<a href="/blog/">GSoC'24 Update Blogs</a>
</div>
<br>
<div style="padding:20px;border-style: dotted">
	<a href="https://summerofcode.withgoogle.com/archive/2024/projects/eyxxsgo0">Project Page</a>
</div>