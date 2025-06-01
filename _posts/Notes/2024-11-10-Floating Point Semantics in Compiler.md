---
layout: post
title: "[Notes] Floating Point Semantics in Compiler" 
---

### Floating Point Semantics
* Floating Point Algebra
  + limitation  Computer Representation of FP and real world Real Numbers
    - Base, Precision
    - infinity
    - NaN
    - Denormal
    - SubNormal
    - Normal
    - Sized Zero
    - Exception
      + Overflow
      + Underflow
      + Divide by zero
      + Invalid
      + Inexact
  + Exception
  + Traps and floating point status register    
  + Rounding Errors
    - Important because results need to be consistent across machines 
    -  Relative Error Method (IEEE FP standard)
    -  ULP Method
    -  Rounding Modes
       + Nearest
       + Positive INF
       + Negative INF
       + Zero
    
* IEEE 754 compliance
* FP model
* Fast Math
  




### Reference
* [FastMath Blog](https://simonbyrne.github.io/notes/fastmath/)
* [FastMath Blog](https://kristerw.github.io/2021/10/19/fast-math/)
* [GCC Blog](https://gcc.gnu.org/wiki/FloatingPointMath)
* [Basic ](https://pandepra.medium.com/basics-of-floating-point-part-1-956ba5f99213)
* [FP demystified](https://blog.reverberate.org/2014/09/what-every-computer-programmer-should.html)
* [dumpfp tool](https://blog.reverberate.org/2012/11/dumpfp-tool-to-inspect-floating-point.html)
* [What every programmer needs to know](https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html)
