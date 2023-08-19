---
layout: post
title: "[Paper] Bring Your Own Codegen to Deep Learning Compiler
" 
---

### Edge Computing
* DNN Requires High Computation and memory requirement, all devices can not communicate to high end servers to compute tensors
* Troubles with network latency and data privacy, hence Edge Computing, execute at end
* Challenges with lower cost and power budget

### DNN Workload
* Types :  Tensors
* Stack of compute intensive Convolution and Matrix Multiplication operators as Data flow
* Complex Control Flow with non linear structure

*  Sorting is challenging 
*  IF control Flow in dataflow architecture is challenging

### Challenges in Deep learning Compiler
* Each Vendors designs its own
	+  Model representation
	+  Optimization Sequence
	+  hardware code
	+  run model inference with graph execution and data transfers

* Paper targets unification of this steps
* Issues with Compiler stack
	+ Execution issues with changes in model 
	+ No automatic workload classification into serial host and parallel accelerattor blocks
	+ Need of reoptimization 
* Solving Issues with Compiler stack
	+ Divide Workload at IR level for different hardware based on cost
	+ Use Annotation from code of model for offloading
	+ Framework of hardware independent, hardware specific and code generation 

#### Graph Partition
* Users can model graph into regions with annotation
* classify accelerator friendly regions and host regions
* Multi-level IR as data strcture for partitioning, Each op with attributes about computation

##### Pattern Based Grouping
*  Match Sequence of Notes and replace it with composite operation 
* TODO
![Grouping]({{site.url}}/public/paper/buildyourbackend/grouping.png "Grouping")

#### Annotation 
* interpreter annotation functions to define metadata
* Use metadata at region calculation and optimizing operation

#### Cost based partition
*  Cost of moving data to accelerator
![flow]({{site.url}}/public/paper/buildyourbackend/flow.png "flow")

### Accelerator Specific Processing
*  Quatization - Data types supported  ->  Reduce resource utilization and this energy
	+  User defined quantization vs compiler concluded quantizatio
*  Layout transformatio
	+ Tensor Layout can reflect on latency of access
	+ Adds layout transformation nodes at partition function boundary 
![Runtime]({{site.url}}/public/paper/buildyourbackend/rt.png "Runtime")

### Notes
*  Classic CNN good to offload with cost based partitioning, same did not work for object detection model like fast R-CNN or SSD
* Operators that cannot group with other compute intesive opse.g. transpose, maximum , resahpe

### References
* [Paper Bring Your Own Codegen to Deep Learning Compiler]
* [Operation Attributes](https://github.com/apache/tvm/tree/v0.13.0/python/tvm/relay/op)
* [Annotation Example](https://tvm.apache.org/docs/dev/how_to/relay_add_op.html)
