---
layout: post
title: "[Cpp Notes] Low Latency C++"
---

* [Original Link](https://www.youtube.com/watch?v=EzmNeAhWqVs&ab_channel=CppNow)
* [Original Link](https://www.youtube.com/watch?v=5uIsadq-nyk&ab_channel=CppNow)
* [Author]()


### Metric 
*  Latency = time taken to Deliver 
*  Throughput = Amount of data per unit time

## Why C++
*  Manual Memory management over hot path
*  Scalable Zero cost abstractions
*  Exisiting frameworks

### Measuring 
*	CPU  - gprof
* Cache	 - cachegrind
* Pipeline 
* Code Size less != fast code
* Benchmarking
	+ Quick bench
	+ Google Benchmark

### Efficient Programming
* Requires knowing
	+ Lang
	+ Compiler
	+ Library
	+ Hardware
* Avoid 
	+ Copies
		- Use References
		- Move loop independant code out of loop
	+ Too Many function calls
		- Inline
		- alternatives to virtual call, Use CRTP or std::variant

	+ Compile time stuff
		- constexpr 
		- Metaprogramming

### Low-level bit manipulation in C++
* Object lifetime rules
* Aliasing rules
* Alignment rules
* Object representations
* Value representations
* std::bit_cast (since C++20)
* Implicit-lifetime types (since C++20)
* std::start_lifetime_as (since C++23)

### Optimizing
* Knowing Undef beh
* Adding attributes

```cpp
int divide_by_32(int x) {
[[assume(x>=0)]];
return x/32;
}
```
* Pipelining
	+ Avoid Branch Mispredict using attribute `[[likely]] [[unlikely]]`
	+ Rewrite Conditionals
	```cpp
	if(x || y) {}  // into
	if(bool(x) + bool(y))	
	``` 

  ```cpp
   sum += cond ? expr1 : expr2; // into
   decltype(expr1) temp[] = { expr1, expr2};
   sum += temp[bool(cond)];
  ```

  + Avoid data hazards

* Autovectorization
	+ Compiler Dependent
	+ works for basic types
	+ Prefer array of structs over struct with array
	+ linear traversal
	+ Loop is for
	+ Compile time iteration bounds
	+ No data dependant breaks
	+ No conditionals or aliasing or data  dependence on iterations  

	```cpp
	int a[1000], b[1000], c[1000];
	for (int i = 0; i < 1000; ++i) {
		a[i] += b[i];
		b[i+1] += c[i];	
	}

	// into
	// Remove index dependance 
	a[0] += b[0];
	for (int i = 1; i < 999; ++i) {
		b[i+1] += c[i];
		a[i+1] += b[i+1];
	}
	b[999] += c[999];
	```
	+ architecture specific dispatch
	```cpp
	__attribute__ ((target ("arch=atom"))) int foo ()
	```

### Cache 
* Minimize data miss
	+ data locality
	+ Alignment of data
	+ Concurrency aspect of sharing data between cores
	+ Good Layout for prefecher
	+ Cache Friendly
		- Data structure  like flatmap
		- Algorith like binary search

* Minimize instruction miss
	+ Code Layout and alignment
	+ Avoid branch and virtual



### Deterministic Time for program
* AVOID this in Hot Path
	+ Dynamic Allocation
		- Data structure or Algo or data types that allocate
		- e.g. `std::stable_sort` allocates
		- e.g. `std::array` or `std::optional` or `std::variant` does not allocate
		- e.g. `std::any` or `std::function` or `std::vector` allocates 
		- Custom allocators not good eg tcmalloc  , rpmalloc for low latency
		- Use pre-allocation 
			+ Monotonic alloctor `std::pmr::monotonic_buffer_resource`
			+ Pool Allocator `std::pmr::unsynchronised_pool_resource`
			+ Frame/ Arena allocators
			+ LockFree Allocators
	+ Blocking call
		- Dont use coroutines 
		- Terms
			+ Atomic = race free
			+ Lock free = min one thread will make progress
			+ Wait free = all threads will make progress
		- No Mutex, COnditional Var or lock or semaphores!
		- Only use `std::atomic` but not spin on it or do `std::atomic<T>::wait/notify`	
		- checking lock free with `static_assert(std::atomic<T>::is_always_lock_free)`
		- Can use wait free q  TODO
		- can use try_lock
		- can use double buffering

	+ IO
		- pre-load and lock address range

	+ Exceptions
	+ Context and Mode switch
		-  pin hot path thread onto one cpu. No hyperthreading
	+ syscall - No
	+ Calling unknw code
	+ Loops without clear bounds
	+ Algo bounds higher than O(1)
