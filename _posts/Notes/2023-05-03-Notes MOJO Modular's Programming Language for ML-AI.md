---
layout: post
title: "[Notes] MOJO Modular's AI Programmig Langauge" 
---

## System Programming Requirements For Programming ML Hardware
* StrongLy typed
* Zero overhead abstraction
* Static Dispatch
* Meta prgramming
* Interpreter but JIT

## Current 
* C++ - Strongly Typed, Zero overhead abstraction (when Properly used) , supports compile time meta programming and compile once for binary, statically compiled
* Python - Overhead with types, dynamically compiled, JIT or AOT , has dynamic meta programming
* Mojo Includes both of to do uses good part of both and intents to superset python 


# MOJO Analysis from Principles of Programming Langauge
* It is python, but with system programming features and restrictions

## Expressions 
*  Uses concept of Value Binding in lambda scope
*  Immutable  Declartion  "let"  - Initial values are computed before any of variables become bound
*  Mutable Declarations "var" 
*  Scope determination with lexing

## Data layout for aggregation Mojo:Struct
* low-level data layout control with explicit defination of instance props with let or var
* No indirection, No pointers so no dynamic dispatch or swizzling
* structs dont gothourgh biding-storage symbol table lookup used in python interprter 
* hence structs are inlined on stack and methods are resolved statically

## Functions in Mojo
* Representation low level programming abstraction 
* python: def is dynamic with mutable arguments,defined  local variables and no scope enforced
* Mojo:fn  , Arguments are immutable ( concept of let), explicit type defination , all locals are explicit
* __copyinit__  and __moveinit__ as copy and move constructors
 
## TDOO

## References 
* [Mojo doc](https://docs.modular.com/mojo/notebooks/)
