---
layout: post
title: "[Notes] Machine Learning Hardware CUstomizations" 
---

Continuation on Essense of Machine Learning Hardware,....

### What is customized for each ML App on Hardware?
* Memory Hierarchy (only interested in memory near ALU)
* IO Communication ( Not interesting enough)
* Data types
* ALUs

### Components of ML program?

* Loop All dimentions and MAC input and weight to produce output

{% highlight cpp %}

kernel void convol1D(int  * in, int * out, int * filter){
	for(int l = 0 ; l < N ; l++)
	   for(int k = 0 ; k < N ; k++)
		  for(int j = 0 ; j < 3 ; j++)
             for(int i = 0 ; i < 3 ; i++)
				out[l][k] = out[l][k] + in[l+r][k+i] * filter [j][i];	

}

{% endhighlight %}

* This is computing one by one row
* We could send  complete row to compute , second forloop by N/group_size aka "Scheduling" 
  Scheduling can include Loop Splitting, loop fusion , loop reorder , loop unroll and pipeline 
* Data layout can be parttioned and packed or restructured with affine mapping
* Kerenl can use low bit representation "quatization"
* During row by row dispatch, buffers can be reused in next iteration, to hide latency (PIPO) "Data Placement"

* ISSUE : if we write xilinx HLS way, customization requires rewrite, programmers manual efforts in order of looping,quatization and buffer placement.

### How to built system that supports customization by machine?
## TVM approach
* Specification of MAC as AST and Shape. 
* TVM declartions accept type customization in python AST
* TVM uses AST as compute for PE and finds out schedule, instead of doing polyhydral transforms for affine ops
* TVM's genetic machine learning algorithms optimizes MAC operation by calculating different loop schedule to finds
first reasponable solution. 

 
## References
* Software Defined Reconfigurable Computing 