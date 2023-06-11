---
layout: post
title: "[Paper] MLIR: A Compiler Infrastructure for the End of
Moore’s Law" 
---

* target Composibility of compiler outputs
* ML graphs to  new Abstraction which can be lowered to target LLVM
* Good engineering for error reporting, easy porting to new hardware and predicatable performance
* Languages like swift also have own IR

## Design 
#### IR Abstractios
1. Which are IR abstractions ?
	+ Basic extensible abstractions of type, operations and attributes
2. What can be customized? 
	+ ML Graph, AST , Polyhydral Loop and Control flow

#### SSA and Region
1. Why SSA ?
	+  Makes sparse and simple dataflow analysis
	+  nested regions as a first-class concept in the IR 
	+  regions can model loops in other IR and machineindependent way, good for heterogenous compilatio
	+ support for CFG based analysis and transformations
	+ llvm has linearized control flow into preheader,header body,latch 	
#### Pregerssive Lowering
1. How?
	+  Progressive from programming models like TF to platforms
	+ e.g. clang has  AST->LLVM IR-> SelectionDAG ->MachineInstr ->MCInst
	2. What are roles of passes?
	+ Optimizing transformation
	+ Enabling Transformation
	+ Lowering
	+ CLeanup

#### High Level Semantics
1. Why ?
	+ Computation once lowered will have to use debug info record
2. How? 
	+ structure of computation and progressively lower to the hardware	

#### IR Validation
*  SIngle source of truth

#### Declarative Rewrite Rules
* Defining representation modifiers should be as simple as that of new
abstractions 

#### Source location tracking and traceability
* propagating high-level information to the lower levels is to help support secure and traceable compilation


### MLIR Details 

```javascript
// Attribute aliases can be forward-declared.
#map1 = (d0, d1) -> (d0 + d1)
#map3 = ()[s0] -> (s0)
// Ops may have regions attached.
"affine.for"(%arg0) ({
// Regions consist of a CFG of blocks with arguments.
^bb0(%arg4: index):
// Block are lists of operations.
"affine.for"(%arg0) ({
^bb0(%arg5: index):
// Ops use and define typed values, which obey SSA.
%0 = "affine.load"(%arg1, %arg4) {map = (d0) -> (d0)}
: (memref<?xf32>, index) -> f32
%1 = "affine.load"(%arg2, %arg5) {map = (d0) -> (d0)}
: (memref<?xf32>, index) -> f32
%2 = "std.mulf"(%0, %1) : (f32, f32) -> f32
%3 = "affine.load"(%arg3, %arg4, %arg5) {map = #map1}
: (memref<?xf32>, index, index) -> f32
%4 = "std.addf"(%3, %2) : (f32, f32) -> f32
"affine.store"(%4, %arg3, %arg4, %arg5) {map = #map1}
: (f32, memref<?xf32>, index, index) -> ()
// Blocks end with a terminator Op.
"affine.terminator"() : () -> ()
// Ops have a list of attributes.
}) {lower_bound = () -> (0), step = 1 : index, upper_bound = #map3}
: (index) -> ()
"affine.terminator"() : () -> ()
}) {lower_bound = () -> (0), step = 1 : index, upper_bound = #map3}
: (index) -> ()
```

#### Operation
* Operation is  Instruction or Function or MOdule, no standard Ops
* Has user defined extensions
* MLIR has support rich Op semantics  
	+ unique  opcode : string "dialetct.OpName"
	+ Operands 0 or more
	+ result  0 or more
	+ LHS is SSA with type
* Ops have
	+ Attributes
	+ Regions
	+ Block Arguments
	+ Location Info
* %-identifiers are (packs of) named values, with “:” specifying the  number in a pack if more than one and “#’ a particular value. 

#### Attributes
* Exensible , not fixed set
* Compile time structured static information e.g. integer constant values
* Attributes are typed
* Op has  <Key,Value> = <String,AttributeValues>

```llvmir
#map1 = (d0, d1) -> (d0 + d1)
%3 = "affine.load"(%arg3, %arg4, %arg5) {map = #map1}
: (memref<?xf32>, index, index) -> f32
```
* #map1 is attribute alias,  (d0,d1)-> (d0 +d1) is affine transform

#### Location Info
* keep the source program stack trace that produced an Op, to generate debug information

#### Regions and BLocks
* op may have list of attached regions
* region is nesting strucutre,Each region has list of blocks
* Each region ends with terminator operation 
* attributes, the semantics of a region are defined by the operation they are attached to
*  blocks inside the region (if more than one) form a CFG e.g. `affine.for`
*  Op specifies the flow of control across regions
* Terminator instruction can be conditional jump,switch or unwind
* It may chose to transfer the control flow to another block in the same region, or return it to the Op enclosing the region. The graph of successors defines a CFG, allowing standard SSA-based control flow within a region
*  No phi nodes, functional SSA with block Arguments 
*  Terminator passes block arugments to successor blocks
* Each block has typed block arguments
* entry block argument as loop induction variable	

#### Value Dominance and Visibity
* Ops can only use values that are in scope i.e visible according
to SSA dominance, nesting, and semantic restrictions imposed by enclosing operations
*  Values are visible within a CFG if they obey standard SSA dominance relationships, where control is guaranteed to pass through a definition before reaching a use

* Scope Barrier using operation "isolated from above",no use-def chains may cross the isolation barriers

#### Symbols and Symbol table
* Ops can have a symbol table attached
* Map from string to IR object 
* symbols are named entities need not obey SSA: they cannot be redefined within the same table, but they can be used prior to their definition.

####  Dialect 
* Logical Grouping of operation and type under same namespace
* Dialect does not have its own new semantics,just grouping
* IR can have mix of dialects

#### Type system
* user-extensible
* may refer to existing foreign type systems like llvm
* enforces strict type equality checking 
* only supports non-dependent types, including trivial, parametric, function, sum and product types
* possible to implement a dependent type system by combining Ops with symbols and user-defined type , will be opaque for MLIR
* standard types : ap_int, ieee floats, tuples, ND vectors, tensors

#### Modules and Functions
* both are ops
* A module defines a symbols. block has its body contains a list of Ops, which may be functions, global variables, compiler metadata,
or other top-level constructs.

* function is op with single region with arg correspoding to function args
* Control is transfered into function with function call op

### IR Infra
#### Operation Description Syntax 
* defines op and components declaratively
* modeling Ops and rewrite pattern

#### Polyhedral code generation
* affine dialect operates on a structured multi-dimensional type for all accesses to memory.
* structured types are injective
* Affine Modelling -2 Parts
	+ attributes are used to model affine maps and integer sets at compile time and Ops used to apply affine restrictions 
* Difference from Poly Framework
	+ Rich Types , Index Space  to Actual Address Space Mapping and less dependent on layout of data
	+  ability to represent the order of loop iterations in the type system	
	+ No linear programming or heuristics

### Concepts
#### Continuation
*  Abstraction that has control state of program
* Concept of co-routines and exceptions

#### Continuation Passing Style
*  Programming style in which control is passed explicitly to in form of continuation		 		

### Reference
* [MLIR Paper](https://arxiv.org/pdf/2002.11054.pdf)
* [MLIR Operation](https://mlir.llvm.org/docs/DefiningDialects/Operations/)
* https://www.cs.cmu.edu/~fp/courses/15411-f08/lectures/09-ssa.pdf
* https://doc.pypy.org/en/latest/stackless.html#continulet
