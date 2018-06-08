---
title: "GSoC'18: Overview"
date: 2018-05-05
tags:
  - GSoC
  - Shogun
---

Projects of GSoC'18 were announced last week and I am happy that I will be working on [Shogun](http://shogun.ml) over this summer. In this post, I am pleased to share some details of my project.

Shogun is a versatile machine learning toolbox that offers a bunch of convinent and unified algorithm implementations. Written in C++ and providing interface in different languages via SWIG, it is a great place to hack.

My project, [Continuous Detoxification](https://summerofcode.withgoogle.com/projects/#6031654070517760), will focus on redesigning and refactoring, aiming at modernizing Shogun's codebase and improving experience for both users and developers. As a project lasting for more than ten years, some parts of Shogun are old and not well-designed. For example, parallel cross validation is not thread safe because features are modified in some algorithms and thus cannot be shared. So, as the first part of my project, I would like to make features immutable. There are several blockers that need to be addressed first. Algorithms that rely on mutation of features will be refactored and preprocessors which are coupled with features need to be redesigned as well. Besides, views of features and labels will be introduced to enable sharing underlying data between features or labels. Some other parts will be taken care as well for thread safety. After we have immutable features, we can safely enable parallel cross validation.

Un-templated matrix and vector are another part, meaning that we will have Matrix and Vector instread of SGMatrix<T> and SGVector<T> and hide the specific premitive type from algorithm implementations. This is a continuation from the project last year. The previous PR uses a big switch to dispatch to typed functions on the fly. Though it has demonstrated a basic approach to implementing typeless data types, it is not very effective as we don't like to switch every time we use it, especailly in a loop. I'm hoping to bring lazy evaluation to make it effective. Inspired by the broad usage of the powerful expression template in several libraries (Eigen, uBlas, mshadow), I will take advantages of it to support lazy evaluation of typeless data types. Note that in Shogun the primary goal we utilize lazy evaluation is to prevent type switches, so the design will be a bit different. I will discuss it in the following posts. After we have implemented something that can put into real use, we can consider refactor of existing code. This will be a huge task and will be done in the future.

To share more details about my project with wider audience outside the community, I will keep myself updated in the following weeks here in this blog as we go on.


### Timeline

Week1 - Week2 : Transformer         
Week3 - Week4 : Pipeline            
Week4 - Week6 : Untemplated linalg

### Summary of PR

Transformer 
[#4285](https://github.com/shogun-toolbox/shogun/pull/4285)
[#4316](https://github.com/shogun-toolbox/shogun/pull/4316)
[#4326](https://github.com/shogun-toolbox/shogun/pull/4326)

Variant
[#4290](https://github.com/shogun-toolbox/shogun/pull/4290)
[#4295](https://github.com/shogun-toolbox/shogun/pull/4295)

Notebook
[#4269](https://github.com/shogun-toolbox/shogun/pull/4269)
[#4273](https://github.com/shogun-toolbox/shogun/pull/4273)
[#4292](https://github.com/shogun-toolbox/shogun/pull/4292)
[#4302](https://github.com/shogun-toolbox/shogun/pull/4302)

Meta Examples
[#4318](https://github.com/shogun-toolbox/shogun/pull/4318)
[#4325](https://github.com/shogun-toolbox/shogun/pull/4325)
