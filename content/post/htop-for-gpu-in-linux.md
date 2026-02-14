---
title: "HTOP for GPU in Linux"
date: 2022-06-12T17:02:57-07:00
lastmod: 2022-06-12T17:02:57-07:00
draft: false
keywords: ["linux", "GPU", "gaming", "benchmarking"]
description: "GPU monitoring tools for Linux: nvtop and gpustat for NVIDIA, intel_gpu_top for Intel, and radeontop for AMD graphics cards."
tags: ["til", "Linux", "command"]
categories: ["linux"]
author: "Harisankar P S"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams:
  enable: false
  options: ""

---

htop/top is a useful command that helps users understand how much of there CPU (Different core)/RAM are being used. We would like to see the same for GPU there are tools that provide that, but sadly unlike cpu top/htop the ones for GPU are sadly fragmented. Based on your GPU vendor we have different tools to provide us this graphical info.

Note: I am talking about tools i tested in Ubuntu/Debian Linux

For nvidia

* nvtop
* gpustat


For intel (iRIS)

* intel_gpu_top (from intel-gpu-tools package)

For AMD GPUs

* radeontop

Link to Reference:

[AMG GPU](https://linuxhint.com/apps-monitor-amd-gpu-linux/)
