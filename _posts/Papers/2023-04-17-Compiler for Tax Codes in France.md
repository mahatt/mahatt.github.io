---
layout: post
title: "[Paper] MLANG Compiler for Tax Codes in France" 
---

* Frenchh Income tax uses compiler to efficiently calculate and reason about taxation
* Why? 
	+ Traditional Workflow is loop like
	+ For Tax_Payer in  All Tax Payers	
		Get_Profile for Tax_Payer
		Execute Tax_Code Sequences
	+ Time consuming and not scalable, Computationally Expensive
	+ Implementing new tax codes is hard

### Solution - Legacy System
*  In 1990, France buuilt Compiler with custom lang for income tax
*  It has Tax Code Rules as M file, which get compiled to C Files, then compiles GCC -> Computation
 M -is a custom, non Turing-complete language. 
	
### Concept

*  New System is called Mlang. It has a formal semantics; 
*  It eliminates solution 1 hand-written workarounds in C
*  It compiles to modern languages (Python)
*  It enables a variety of instrumentations, providing deep insights about the essence of French income tax computation. 
* New edition of the Tax Code describes in natural language
* Tax Cod as bracket system redits, deductions, optional rules, state-sponsored direct aid, all of which are parameterized over the composition of the household, that is, the number of children, their respective
ages, potential disabilities etc.


 
## Improvement in Solution -  New System
* Formal semantics for the M DSL, along with a proof of type safety per-formed using the Coq proof assistant 
* Eliminate C code generation by adding capabiliutes to M 
* Implement reference interpreter along with an optimizing compiler that generates C and Python code 
 

## M Lang and M++
*  Declarations
  + Floats or undef 
  + input variables (scalar or fixed length array), intermediary variables, output variables and exceptions
* Rules  - Computation part, assignments or Raise-if stmt

* More on GIthub Specs

## Compiler Details
![Reductions]({{site.url}}/public/mlang/reductions.jpg "Reductions")
![Flow]({{site.url}}/public/mlang/compilation.jpg "Compilation") 

### References
* [Rules](https://gitlab.adullact.net/dgfip/ir-calcul/-/tree/master/sources2011m_9_6)
* [Conq]()
* [Paper](https://arxiv.org/pdf/2011.07966.pdf)
* [Mlang](https://mlanguage.github.io/mlang/mlang/index.html)