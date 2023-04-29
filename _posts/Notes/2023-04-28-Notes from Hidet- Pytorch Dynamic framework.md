---
layout: post
title: "[Notes] Notes from Hidet-Pytorch Dynamic Framework "
---

* Simplification of deep learning operators on ML accelerators
* Good For implementing extremenly optimized custom operators
* Graph Level Optimizations and Operator Level Kernel Tuninh
* Easy pattern specification fo fusion
*  Tensor Program (hidet.ir.compute) 
   + Embed Scheduling Process as Task(hidet.ir.task) Mapping to define Computation Assignment and ordering
   + Fine granularity allows program  statement-level  optimization
   
* Proposes new Post-Scheduling fusion optimization  
* efficient hardware-centric schedule space , indepedent of  program input size

* Scheduling  
  + Rule Based 
    - hidet/ir/dialects/pattern.py
  + Template Based
    - Good If More Reductions
    - implement_cuda() as Interface in Task Derived Class
	- Define Hidet.script Module Define Load_reg,Store Reg and operation 
	- Complete IR module
	- Define Operator Derived class
	
Summary :
*  MLIR - static compilation, TVM -  dynamic shaping for tensor based on user choice with dynamic codegen. Hidet goes further TVM to implement IR in python and pattern maching in python, it can also accept matching,scheduling operators closure from users.

* [Hidet](https://pytorch.org/blog/introducing-hidet/)
* [CentML](https://centml.ai/)
* [Paper Hidet](https://dl.acm.org/doi/pdf/10.1145/3575693.3575702)


 










