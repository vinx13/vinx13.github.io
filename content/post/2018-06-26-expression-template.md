---
title: Lazy Evaluation with Expression Templates (1)
date: 2018-06-26
tags:
  - GSoC
  - Shogun
---

In last post I talked about the ongoing refactor of linalg.
So we are going to have untemplated vector and matrix types, using a void pointer and checking type information in runtime.
Say you have some vectors and do some computations like
```cpp
Vector a, b, c;
c = add(add(a, b), c);
```
A simple idea is to switch over element types of vectors and call templated routine `linalg::add<T>`.
In this way we will have to switch every time, over each expression involved, e.g, a, b, c and a+b.
This is not desirable not only for runtime overhead but also much boilerplate code we will have to add.

To prevent redundant big switches every time, lazy evaluation is a quite nice idea here. Let's start with a simple `Vector`:
```cpp
class Vector {
public:
    Vector(void* data, EPrimitiveType ptype);
    operator SGVector<T>();
private:
    EPrimitiveType ptype;
    void* data;
};
```
It has templated conversion operator, which allows conversion to `SGVector<T>` that shares the underlying memory.

To support lazy evaluation, we define expression types to represent the expression to be evaluated such that a expression tree consisting of multiple expressions can be evaluated at one time.
```cpp
template <typename E>
class Exp {
    E& self() { return *static_cast<E*>(this) }
    const E& self() const { return *static_cast<const E*>(this); }
}
```
Note that `Exp` is a templated class since we are using CRTP pattern to support static polymorphism.

One of the expression types we have is `VectorExp`. By `Vector` it means that it evaluates to some `Vector`. Similarly we can have `MatrixExp` and so on. Then we can derive specific types of vector expressions., for example `BinaryVectorExp` is a template class that represents some binary expressions with a custom operator.
```cpp
template <typename E>
class VectorExp: public Exp<VectorExp<E>>;

template <
    typename OP, 
    typename E1, 
    typename E2
>
class BinaryVectorExp: public VectorExp<BinaryVectorExp<OP, E1, E2>>;
```
Here `OP` is an operator class that defines the templated evaluation process. For example, vector addition can be defined like:
```cpp
struct VectorAdd {
    VectorAdd(double alpha, double beta): alpha(alpha), beta(beta) { }

    template<T>
    SGVector<T> apply (
        const SGVector<T>& a, 
        const SGVector<T>& b
    ) {
        return linalg::add(a, b, alpha, beta);
    }

    double alpha, beta;
};
```

Now we can put things to an expression. An expression may contain simply a vector, or one or more expressions and an operator. The problem we have now is how we put a vector to an expression since it is an expression actually. This is important since it is reasonable that `add` can accept both `Vector` and `VectorExp`. We define the expression type, `VectorRefExp`. By wrapping a `Vector` instance into a `VectorRefExp`, we are able to use vector as other expression types when writing an expression. 
```cpp
class VectorRefExp: public VectorExp<VectorRefExp>
{
private:
    Vector vector;
};
```

The last step for this is to define a wrapper method can accept either `Vector` or `VectorExp`. Let's continue with the vector addition example above.
```cpp
template <
    typename E1,
    typename E2
>
auto add_impl(VectorExp<E1> e1, VectorExp<E2> e2, double alpha, double beta)
{
    return BinaryVectorExp(VectorAdd(alpha, beta), e1.self(), e2.self());
}

template <typename... Args>
auto add(Args&& args...)
{
    return add_impl(forward_exp<Args>(args)...);
}
```
In this example, arguments are forwarded to `add_impl` and cast to `VectorRefExp` whenever `Vector` is passed by `forward_exp`.

Now we could write down our lazy expression like:
```cpp
Vector a, b;
// do some initialization
auto c = add(a, b); // c is an intermediate expression
Vector d = add(c, a); // implicitly trigger evaluation when assigning to a vector
``` 
How do we evaluate such an expression? Let's talk about it in the next post.