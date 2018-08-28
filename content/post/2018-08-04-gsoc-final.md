---
title: "GSoC'18: Final Review"
date: 2018-08-03
tags:
  - GSoC
  - Shogun
---
We are near the end of GSoC this year.
It has been a really successful and exciting summer.
New features have been added to Shogun.
Besides, we have get lots of old things cleaned up and improved.
This post is the final review of the project.

My work during this summer mainly has two parts: immutable features and untemplated linalg. The former one is consisted of a series of refactor and redesign towards immutable features, including transformer and pipeline, dropping non-const methods from features. The topic about untemplated linalg is to drop the type argument `T` from `SGMatrix<T>` and `SGVector<T>` and put the type information in runtime.

#### Pipeline
Pipeline is a machine that chains multiple transformers and machines.
It consists of a sequence of transformers as intermediate stages of training or testing and a machine as the final stage.
Features are transformed by transformers and fed into the next stage sequentially.

The pipeline instance is built using the `with` and `over` in PipelineBuilder.
We use `with` to add transformers and then finalize with a machine passed to `over`, which returns a `Pipeline` instance.
As `Pipeline` inherits directly from `Machine`, we can use it in exactly the same way as machines.
This means that it supports the `train` and `machine` interface and we can also use it in cross validation.

Internally stages are <name, pointer> pairs, where pointers are `CTransformer*` or `CMachine*` stored in `variant`.
Since `variant` is available since C++17, I also introduced a C++11 compatible `variant` library to Shogun.

#### Transformer
The new transformer class is a combination of preprocessors and converters that provides a unified interface for features transformations.
It supports standard `fit` + `transform` interface.
Plus, it can also work in in-place mode that operate on the same features instance and prevents unnecessary copy.

With the new transformer interface, I removed old preprocessor APIs from features. Previously preprocessor functionality is mixed up in features. 
For example, it maintains states of preprocessors added. 
`apply_preprocessor` mutates data of features is removed this is equivalent to the in-place mode of transformers.
Removing them makes features much cleaner.

`inverse_transform` is a new feature to Shogun.
There are some transformers that are possible to support this feature, for example, `ICAConverter`, `LogPlusOne`.
The inverse mode of these transformers are easy to implement. The future work will be implementing inverse mode for these transformers.

#### Custom Exception type
Previously, `ShogunException` is thrown whenever an assertion fails or `SG_ERROR` is explicitly called.
This error handling way is not optimal because the exception type does not provide any additional information.
Users have to read the error message to figure out what the error is, which is not friendly to automated tests.
New macros `REQUIRE_E`, `SG_THROW` are introduced that support custom exception type.
They work basically in a similar way with `REQUIRE` and `SG_ERROR`, except that they both accept one extra argument to specify the type of the exception to throw.
Using these macros to throw proper exceptions all over Shogun would be a good idea for future work.

#### Features and Labels View
The template function `view` is added to a new instance of features or labels that shares a subset of data.
Previously when we need to operate on a subset of data, we directly call `add_subset` and then `remove_subset`, which makes features and labels mutable.
`view` creates a shadow copy instead. This is a cool thing that we can make features immutable while still don't need to waste time copying data.
Also, the non-const subset APIs can be hidden from public interface of features.

#### String Features Factory
Two factories of string features are added.
This makes it possible to create string features and string embedding features from SWIG interface so that we can continue porting more Python examples to the meta example system.
`string_features` is similar to the factory for dense features. It creates string features from files.
A overloading method of `string_features` accepts string features and extra parameters for embedding provides facility for mapping string features to high order representation.

#### Untemplated Linalg
Making linalg untemplated is to store data in a void pointer and put the type information in runtime.
We would like to move the template argument `T` of data types (`SGVector` and `SGMatrix`) and linalg to runtime and let the algorithms to check the type.
When we are using untemplated data, such as Vector and Matrix, we have to check the type, which is a enumeration value, and do the switch.
This way is not very effective since we will have to do this again and again.
Therefore, based on the PR last year, I implemented lazy evaluation with expression templates.
Now, multiple operations, for example an addition and a multiplication, can be chained and then evaluated at once.
And we only need to check the type information once.
This prototype still need improvement.
More details can be found in my previous posts.
In the future, we can continue from this prototype and finally use it in Shogun.

#### The Story  
Shogun is my first open source participation. 
When I was been seeking for open source opportunity, I came across with Shogun and started coding.
Not only for my interests on machine learning but also for that Shogun is super friendly to newcomers (Special thanks to Heiko who helped me greatly during my initial involvement :), I found out that Shogun is the best place for me and later I took part in the GSoC program.

The most exciting and challenging part is to come up with my own design and then implement it, get it merged to the codebase.
Even if we have initial design through discussions, much effort is necessary to elaborate and polish it.

Anyway, what is really important is to be active and creative. Always be ready to communicate, and always write down the code and try it out whenever you have an interesting idea. This makes your coding process much enjoyable and you will find out that it is easier that you thought.

There are still many things in Shogun to improve.
For example, we still need significant refactor on machines and features to make features immutable and finally support better cross validation.
I'm hoping to continue my contributions to Shogun after GSoC because I'm pretty happy with the journey so far :)
For perspective GSoC students, I'm also looking for your contributions.

Finally, I would like to thank my mentors Viktor and Heiko, and all the helpful people in the community, who made this GSoC so successful and exciting.
