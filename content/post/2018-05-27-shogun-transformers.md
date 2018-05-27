---
title: New Transformers in Shogun
date: 2018-05-27
tags:
  - GSoC
  - Shogun
---

The first two weeks of GSoC have ended. Here I will share my progress so far.

In the first two weeks, I have been mostly working on the new transformer class, `CTransformer`. It is introduced mainly for two purposes: 1) Providing generic and unified interface for data transformation. 2) Make features immutable.

In Shogun, we have preprocessors and converters, both operating on features and applying some transformations. The major distinction between them I think is that preprocessors support on-the-fly evaluation: we can add several preprocessors by `add_preprocessor` and then apply them when calling `get_feature_vector`. Other than that, they mostly do the same thing. So it would be good if they provide unified interface. The story is, in machine learning, we always work with either features or labels, that everything can be fit or apply. To this end, transformers are introduced as an abstraction of of converters and preprocessors with two-staged API: fit + apply.
```
    void fit(CFeatures* features);
    void fit(CFeatures* features, CLabels* labels);
    CFeatures* apply(CFeatures* features, bool inplace);
```
The transformers learn model parameters from features and possibly labels during the fit stage. For those that do not need initialization, the fit can be implemented simply as a no-op. 

In apply, it takes some features as input, performs the transformation and returns the result feature which is a brand new `CFeatures` instance. The reason that a new CFeatures is always created is exactly that features are (or should be) immutable. This is also the first step of refactoring towards immutable features. On the other hand, efficient transformers are desirable. It would be disappointing if we need to copy data again and again. Hopefully, most transformers support in-place mode. In this situation, although new features are created, the underlying data (e.g matrix, string list) are shared, which minimizes the overhead for immutable features.

The implementation is actually very tricky to support the in-place option. For example, in the `DensePreprocessor` case, we obtain the feature matrix and possibly create a clone if not in-place. After performing transformation on the matrix (we have `apply_to_matrix` implemented in every subclass of `DensePreprocessor`), we create a new `DenseFeatures` instance with the existing feature matrix and then unref the input features. Unfortunately the ref counting here has brought me much trouble and I have wasted much time here :)

### A brief summary of PR

* [\#4290](https://github.com/shogun-toolbox/shogun/pull/4290) 
  [\#4295](https://github.com/shogun-toolbox/shogun/pull/4295) :
  We can use `variant` in Shogun now. Based on whether c++17 is available, it will choose either std::variant or mpark::variant

* [\#4292](https://github.com/shogun-toolbox/shogun/pull/4292) 
  [\#4302](https://github.com/shogun-toolbox/shogun/pull/4302):
  We finally have `RealFeatures` dropped in all notebooks. Besides, tag-based getters / setters are used in more cases.

* [\#4285](https://github.com/shogun-toolbox/shogun/pull/4285) : a very huge PR for transformers :)

