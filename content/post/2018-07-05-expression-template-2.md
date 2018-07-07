---
title: Lazy Evaluation with Expression Templates (2)
date: 2018-07-05
tags:
  - GSoC
  - Shogun
---

**Progress so far**  
Previously we have implemented lazy expressions using expression template. With expression APIs, we could seamlessly use expressions as vector and matrix.
For example, 
```cpp
Vector a, b;
auto c = add(a, b);
Vector d = add(a, c);
```
In this example we create a addition expression of a and b, and then call `add` with an expression and a vector. 
In the second `add` expression, the argument `c`, which is a `Vector`, will be converted to a `VectorRefExp`.
Therefore, we can pass either plain data type `Vector`, `Matrix`, or expressions as arguments and we only need to deal with are expressions since we have implicit conversion from `Vector` and `Matrix`.

**The problem**  
How to evaluate such an expression? 
Starting from one expression as root we go down to evaluate left hand side and right hand side sub-expressions until we reach some `VectorRefExp` that does not have sub-expressions any more.
Since the whole process of evaluation happens in one time, it's possible to know the type information of all expressions involved.
We hope to query type information of each expression only once when we start evaluation. For a `VectorRefExp`, we could just return the type of the underlying vector.

What should we do for other expressions where the vector or matrix are actually not available?
Similar to evaluation, we could also recursive query the type information until we hit the bottom.
So we define a member function,
```
EPrimitiveType Exp::ptype() const;
```
that returns the primitive type of either the vector or matrix wrapped, or inferred type based on types of sub-expressions and the operation.
Hopefully in this case type can be inferred so we don't need to evaluate every expression involved.

With type information of every expression available, we are now able to evaluate these lazy expressions. We check the type and call typed linalg routine. Then we construct untemplated `Vector` with the typed result (some `SGVector<T>` instance).

**Full example**  
The full implementation for `BinaryVectorExp` is provided below. Note that we have both untemplated and templated method `eval`, which allows us to check the type in runtime and implement the actual evaluation with type information in `eval<T>`.
```
template <class OP, class E1, class E2>
BinaryVectorExp<OP, E1, E> : 
    public VectorExp<BinaryVectorExp<OP, E1, E2>>
{
    // ...
    EPrimitiveType ptype() const
    {
        ASSERT(lhs.ptype() == rhs.ptype());
        return lhs.ptype();
    }
 
    Vector eval() const
    {
        switch (ptype())
        {
            case PT_FLOAT64:
                return Vector(eval<float64_t>());
            case PT_FLOAT32:
                // ...
        } 
    }

    template <typename T>
    SGVector<T> eval() const
    {
        auto lhs_result = lhs.self().template eval<T>();
        auto rhs_result = rhs.self().template eval<T>();

        return op.template apply(lhs_result, rhs_result);
    }
};
```

**What's next?**  
We still have many problems to solve. For example, how to perform in-place operation. Expressions like `v = scale(v, 1.5);` can obviously performed in-place. One way is to overload the assignment operator above. But how do we deal with complex expressions which are DAGs actually. In this case we mush be careful not to overwrite data that will be used in other expressions. 

Another problem is whether we need untemplated scalar. Wrapping a scalar definitely means much overhead. However otherwise how can we implement `ScalarExp::eval` like that in `VectorExp` since we don't know the return type. Maybe we can implement a conversion operator to any primitive type like `float64_t` that triggers evaluation and then casts whatever types of result to the required type.

Currently I don't have a better design, so I would see if we can somehow solve them in the future.


*The demo is available in https://github.com/vinx13/shogun-untemplated-demo.*

*This work is inspired by [mshadow](https://github.com/dmlc/mshadow), [ublas](https://github.com/boostorg/ublas) and [nGraph](https://github.com/NervanaSystems/ngraph).*