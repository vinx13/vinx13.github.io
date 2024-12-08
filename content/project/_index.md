---
type: project
---

# Projects

## MLC-LLM: Machine Learning Compilation for Large Language Models
MLC-LLM is a universal solution that allows any language models to be deployed natively on a diverse set of hardware backends and native applications, plus a productive framework for everyone to further optimize model performance for their own use cases.

* [Project Page](https://llm.mlc.ai)
* [Repo](https://github.com/mlc-ai/mlc-llm)

## TVM: End to End Deep Learning Compiler Stack
{{<image-box "/img/stack_tvmlang.png" >}}
TVM is an open deep learning compiler stack for CPUs, GPUs and specialized accelerators. It aims to close the gap between the productivity-focused deep learning frameworks, and the performance- or efficiency-oriented hardware backends.  

I am an active contributor to TVM.
I have been focused on TOPI quantized operators and graph optimization in Relay IR.
I contributed a full set of quantized operators for CNN on CUDA. Integrated with automatic tuning, these operators on CUDA achieved competitive performance compared with other frameworks such as cuDNN and TensorRT.
Relay is a new high level intermediate representation of the computational graph.
The graph optimization eliminates unnecessary computation by combining and fusing parallel branches.
I strive to reduce the difficulty of deployment of deep models and bring deep learning everywhere.

* [Project Page](https://tvm.apache.org)
* [Repo](https://github.com/apache/tvm/)

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


