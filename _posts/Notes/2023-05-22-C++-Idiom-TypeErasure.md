---
layout: post
title: "[Notes] C++ Idiom : Type Erasure" 
---

* Design Problem : Expose a function F with a argument A (any type) which is not polymorphic but has polymorphic callable interface inside A, which is called inside F.

* A can be CV qualified too.
* F can not be template function since we need uniform  `void F(Atype A)` for every type it is used for.

* Dynamic Polymorphism = Different objects at runtime behave via single "interface" 
* Take "interface" , use any object dynamic or static
* e.g. In C, void* is generic object 

```c
void qsort(void *ptr, std::size_t count, std::size_t size, cmp);
```


* In C++, Templates!   ==> TYPE ERASURE APPLIES WHEN GENERICS is USED!

* Why erase types?
    + We have interface `doWork(Object obj)`, which can accept any Type of Object and simply doWork without losing strong typing 
    + Mostly useful for library development
    
### 3 Steps of Type Erasure Solution



1) Interface that isn't tied to concrete type


```cpp
#include<bits/stdc++.h>
class AbstractMojo{
    
    virtual void justMojo() =0 ;  // Have Delegation
    
    virtual ~AbstractMojo() = default; 

};

// How to call?
void DoMojo(AbstractMojo& jomo);

```
* One Parent Abstract class per interface!
* concrete classes has to derive from ``AbstractMojo``, what if Concrete classes are "Closed for update"
* Requirement : Typewise function call -> function template


```cpp
class AbstractMojo{   
    virtual void justMojo() =0 ;  // Have Delegation
    virtual ~AbstractMojo() = default; 
};

// How to call?
template<typename T> 
void DoMojo(T& jomo);
```


2) Erase Type T

* No such thing as removing type (like assignming to void*) , we can add class abstraction

```cpp

class AbstractMojo{   
    virtual void justMojo() =0 ;  // Have Delegation
    virtual ~AbstractMojo() = default; 
};

class Mojo  : AbstractMojo {  // Provide interface

public:
    template<typename T>    // Templatized  
    explicit Mojo(T mT) :{}
    

    void justMojo() override {  // Provide uniform interface
        mT.justMojo();
    }
    
    //>>>> mT
}

// How to Call
void DoMojo(Mojo jomo);   // Type Erased!

```
3. Storing Erased Type 
* Move T storage to wrapper, it knows type, it has to call interface, it derives from AbstractMojo


```cpp
#include<bits/stdc++.h>
class AbstractMojo{   
    public:
    virtual void justMojo() =0 ;  // Have Delegation
    virtual ~AbstractMojo() = default; 
};

template<typename T>
class MojoWrapper : AbstractMojo { // Provide interface
    T mT;
    public:
    explicit MojoWrapper(T nT) : mT(nT) {} 

    void justMojo() override {  // Provide uniform interface
        mT.justMojo();
    }

    
};

class Mojo {  
std::unique_ptr<AbstractMojo> mWTPtr ;  // STORE ABSTRACT Level 1
public:
    template<typename T>    //still  Templatized  
    explicit Mojo(T mT) {
        mWTPtr = std::make_unique<MojoWrapper<T>>(mT);  // CONSTRUCT CONCRETE , STORE in WRAPPER
    }
    
    void justMojo(){  // Provide uniform interface but not override
        mWTPtr->justMojo();
    }
    
    
};

// How to Call
void DoMojo(Mojo jomo);   // Type Erased!

```

* Example of `std::function`


#### Reference
* [Type Erasure, long Complex](https://quuxplusone.github.io/blog/2019/03/18/what-is-type-erasure/)
