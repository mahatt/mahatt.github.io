---
layout: post
title: "[Notes] Notes Relay IR from Apache TVM " 
---
* designed for ASIC/FPGAs for dynamic 
* Uses  Pure Functional, statically Typed to support efficient compilation, portability and expressive ness
* User models are computation graphs representing differentiable computation

* Tensor FLow 
  + does static graph, easy to optimize, not computational expressive as HLL and have dataflow graph with fix topology at compile time,
  + TF has restricted Control flow, can not adpot to shape of compute graph.  ( Define-then-run Model)

* PyTorch 
  + does graph with dynamic topologies  by using runtime data and add imperative computation. GPU works good. Not Accelerator or FPGA when needs 
   deployement with python (Define-by-run Model).
  + dyanamic framework control flow is lost due to software boundary -> reduced optimization chances 
  + re-optimize any time the graph topology changes, costing CPU cycles and the overhead of moving data between the host and accelerators 
   pytorch solved it by adding IR abstraction (solution : python -> IR , same  as static grpah)
  + Expressive for user, limits ability for existing frmwrk to optimize user defined graph
  
## Relay IR
* Pure Functional Programming Lang for differentiable computation only.
* Functions as first class objects
* Rich Type system dependent on shape
* Automatic differention operator accepts lamba function/closure as input 
* Adds operators from dense linear algebra .g. GEMM, Convolution 
* uses Algorithmic scheduling over polyhydral in Glow or LLVM
* Evaluator for debugging
* type specialize operator building compiler
* Use of "let binding" from PL 

* TVM framework [ functional IR , Type system , Synthesis , CodeGen-Rewrites , Compiler Classis]
*  Tensorization- optimizer decomposing and matching programs to the underlying hardware tensor operations  (Compiler : Vectorization)
*  Operator Fusion (Compiler: Peephole)
*  Layout Change (Compiler: Loop Optimizations) invertible affine transformation
*  Memory Reuse at Graph and Operator Level (Compiler: minimize buffer/register spill)
*  Latency Hiding (Compiler: Interface Optimizations)

* Whole Program	representation  with deep learning programs allows at user level
 + host slicing 
 + Dynamic Networks
 + Change of Layouts
 + Latency Hiding 
 + Parallel and Distributed Scheduling

# Frontends
* Text AST written in Python or C++ 
* Python Frontend -> Adds decorator annotation  "relay" and "relay_model" 
* JSON serialization

# Automatic Differentiaton
For function(x,x")--> Carry both values over All instruction in Function, calculated forwardly.

# Scheduling
* scheduling primitives - loop transformations, inlining, vectorization,...
* All possiblities in Transformations create search space , ML Genetic Algorithm
  - Start with Empty  Policy
  - calculate fitness score
  - Generate Mutation to existing known policy and calculate fitness score
  - Update bag of Population to fitness score , repeat again
  - flops is silent measure for time cost
 
* Search Policy 
  - Empty  naive input 
  - Sketch policy  = Genetical evolution with Cost model and rules

## References
* [Lang Ref](https://tvm.apache.org/docs/reference/langref/index.html)
* [HLS and Microarch](https://tvm.apache.org/docs/topic/vta/dev/hardware.html#microarchitectural-overview)
* [CodeGenerator](https://tvm.apache.org/docs/dev/how_to/relay_bring_your_own_codegen.html)
* [Relay IR](https://arxiv.org/pdf/1810.00952.pdf)
* [Design and Architecture](https://tvm.apache.org/docs/arch/index.html)
* [Walkthorugh](https://tvm.apache.org/docs/dev/tutorial/codebase_walkthrough.html)

 



 










