---
title: "GSoC'18: Peer Review for Inside the Black Box"
date: 2018-08-15
tags:
  - GSoC
  - Shogun
---
This is my review for Shubham’s project, Inside the Black Box ([project page](https://summerofcode.withgoogle.com/projects/6010966421012480), [final report](https://shubham808.github.io/blog/gsoc'18/Final-Report)) this summer.
His work covers some important topics about user experience and internal implementation of Shogun.

#### Main Idea
[`StoppableSGObject`](https://github.com/shogun-toolbox/shogun/blob/develop/src/shogun/lib/StoppableSGObject.h) extends the premature stopping machine last year.
The macro [`COMPUTATIONS_CONTROLLERS`](https://github.com/shogun-toolbox/shogun/blob/41888fe7c8dc3797063d674f452e13351f321338/src/shogun/lib/StoppableSGObject.h#L18) makes it possible to respond to [signals](https://github.com/shogun-toolbox/shogun/blob/41888fe7c8dc3797063d674f452e13351f321338/src/shogun/lib/StoppableSGObject.cpp#L24) during training.
This a helpful since we user can interrupt the training and then possibly resume. Moving these things to the base class `StoppableSGObject` makes `CMachine` class much cleaner.
The new macro `SG_PROGRESS` is very nice. It makes progress bar more informative while requires few [changes](https://github.com/shogun-toolbox/shogun/pull/4305/files).
To add a prefix like `class_name::method_name` to the progress bar, we only need to replace `progress` with the macro `SG_PROGRESS`.

I like the [`IterativeMachine`](https://github.com/shogun-toolbox/shogun/blob/develop/src/shogun/machine/IterativeMachine.h) interface.
It allows us to take control of iterations during computation.
Also, we are able to test against reference result during iterations.
For example, we can pause training every five iterations and test the model parameter against the reference and the resume training.
This would be a good future work idea. The implementation of IterativeMachine class looks very clean and nice.
IterativeMachine is a [mixin](https://en.wikipedia.org/wiki/Mixin) class (i.e. `template <T> class IterativeMachine: public T`). I would like to see some explanation on making T a base class here.

Feature type dispatching utilizes the [CRTP](https://en.wikipedia.org/wiki/Curiously_recurring_template_pattern) pattern to remove [the redundant code](https://github.com/shogun-toolbox/shogun/blob/18cf07f5c4b533a03f438202bbd8fa33f2c91301/src/shogun/classifier/LDA.cpp#L79) for type switch and dispatching to templated method call.
It is clear and easy to follow so that others can port other algorithms to this pattern.
I think it would be good to mention the changes to [`CMachine` class](https://github.com/shogun-toolbox/shogun/blob/41888fe7c8dc3797063d674f452e13351f321338/src/shogun/machine/Machine.cpp#L60) in the blog post.
In [`train_machine_templated`](https://github.com/shogun-toolbox/shogun/blob/41888fe7c8dc3797063d674f452e13351f321338/src/shogun/classifier/LDA.cpp#L62), it returns a boolean to indicate whether the feature type is supported.
I think we could also throw an exception here.
But since currently `train_machine` should return false it training is not successful, this is consistent with the CMachine api.
Using feature type dispatch allows us to support more primitive types other than float64, however if we port them to templated version, I am curious about the overhead of binary size and compilation time due to template instantiation.

#### Conclusion
Overall, Shubham has done a great set of work.
These features help improve user experience and makes Shogun more flexible and versatile.
Besides, the future work mentioned is very helpful for others who would like to continue this work.
I am very excited to see these new features added.

#### Response for Shubham's review
API like 
```
p.over("submean", transformer("PruneVarSubMean")).then("kmeans", machine("KMeans"))
```
is possible in Python and other interface.
This is currently not possible in meta examples because of the limit of the translator.
It would be good to support it in the future.

Using custom exception and the macros `SG_THROW` and `REQUIRE_E` all over Shogun is a very nice idea for future work.

In untemplated linalg, implicitly evaluation happens when you assign an expression to a vector or a matrix.
In this example, we declared the result type using `auto`, which means that we will save the result of `add`, an expression, to the result variable and the expression will be lazily evaluated.
If we use `Vector` instead of `auto`, implicitly evaluation will happen immediately.

Thanks for the suggestion about listing future work.
I agree that this is important to help others to follow my work.
