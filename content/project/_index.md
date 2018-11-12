---
type: project
---

# Projects

## TVM
{{<image-box "/img/stack_tvmlang.png" >}}
TVM is a end to end deep learning compiler stack for CPUs, GPUs and specialized accelerators. It aims to close the gap between the productivity-focused deep learning frameworks, and the performance- or efficiency-oriented hardware backends. As a contributor and reviewer in TVM, I implemented TOPI quantized operators on CUDA. I also work on graph optimization in Relay.

* [TVM Project Page](https://tvm.ai)
* [TVM Repo](https://github.com/dmlc/tvm/)

## Shogun Machine Learning Toolbox
{{<image-box "/img/shogun.png" >}}
The Shogun Machine Learning Toolbox is devoted to making machine learning tools available for free, to everyone. It provides efficient implementation of all standard ML algorithms. Shogun ensures that the underlying algorithms are transparent and accessible—a unified interface provides access via many popular programming languages. 

As a Google Summer of Code participant in this project, I continued the ongoing detoxification of Shogun, and tackled a
number of the long term design issues with the low level library.
Shogun now has a clean transformer and a pipelining API using the latest C++ features. I also worked on improved exception handling, better subset data support, and some design drafts, such as a expression templates for
Shogun's linear algebra.

* [Shogun Project Page](http://shogun.ml/)
* [Shogun Repo](https://github.com/shogun-toolbox/shogun)
* [Google Summer of Code Project Page](https://summerofcode.withgoogle.com/projects/#6031654070517760)  
* [Blog posts](https://wuwei.io/tags/shogun)

## Recurrent Residual Module for Fast Inference in Videos 
{{<image-box "/img/rrm.png" >}}
Recurrent Residual Module is a module to accelerate the CNN inference for video recognition tasks.
This module utilizes the similarity of the intermediate feature maps of two consecutive frames to largely reduce the redundant computation.
One unique property of the proposed method compared to previous work is that feature maps of each frame are precisely computed.
Experiment results show that our module yields significant inference speedup while maintain similar recognition performance. 

* [Paper in CVPR'18](http://openaccess.thecvf.com/content_cvpr_2018/papers/Pan_Recurrent_Residual_Module_CVPR_2018_paper.pdf)

