---
layout: post
title: "[Musing] Essense of machine learning hardware" 
---

Here are few design considerations for machine learning asic. Design traits will achieve efficiency/effectiveness per watts.
(Tacit Ideas) 
  
### Design Paramter : ML Model
* Feed-forward ANN are faster since no data feedback simplfied desgin
* LSTM require accumlation of partial results, increases complexity
* Type of Neural Network affects simplifications to hardware, Usage of domain knowledge.

### Design Parameter :  Hardware use
Common Sense Idea : Inference and Training hardware are different (most time) 
* Hardware for training  includes  input with large dataset, cost function , optimizations function and  model 
  + Large dataset implies more memory for features, bag of features
  + cost and optimzation function use are feedback to improve model with indentifying error. implies "latency of feedback".
  + Support for debugging model to look for training errors, generalization errors  implies "profiling interfaces" 
  + Should have support for workflows
* Hardware for inference
  + It includes input as dataset, not as large as training dataset and model.
  + Model can be compressed based on domain of use.

### Design Parameter:  Input Dataset 
Common Sense Idea: Use known information to minimize data set
* Use of Encoding.
* Use of  Precise data storage. Less Register Size  implies better energy/storage efficiency
E.g. ML Model with human Age as input parameter. Ideally is 32 bit int.
but age of human is < 100  implies 7 bit size.
After understanding use cases, model only understand age ranges with widht of 10. implies 4 bit data.
* Use of Data quantization.
e.g. bfloat16 or MSFP (great common sense idea)

### Design Paramter : Signal Data Processing
* For Vision, audio signals as input, FFT has saved computation significantly!!! ( WOOWWWW!) 
  

### Design Parameter : ALU (PE) Computation 
* List All Unit Operations and minimize to bare minimum required. implies minimum area
* For each operation minimize register size -> aguments datatype 
* For each operation minimize microcode with basics
e.g. MAC Design  (Multiply and Add)
    MAC for  N bit x 2 regs, with result if 2N muls and add to N again
	Less Register Size, less microcode implies better energy/storage efficiency
* Unit Operations in ALU
  + Granularity of Unit Operation designed  in hardware should be Maximizing paralization,
	at cost of space, later in time.
  + Examples of Unit Operation
    - Convolution aka (dot product) , Matrix Multiply.  (Learnings from CPU based MatMul)
      lead to systolic execution of MatMul (MAC) over General MatMul (GMEM).
    - Pooling  aka ( Normalization) , thresholding  (Learning from Comparator design in HDL)
	  Creating minimized Comparator logic ckt. (Spatial Reduction Case)

### Design Parameter  : MOV data
*  More Cycles to get data, implies More stalls in ALU, implies high Energy
*  More frequent access to  to distant memory in Memory heirarchy, Explosively increases energy consumption
*  Always Pipeline data, Move data in Spatial and Temporal Ways.
   + Machine Learning Programming Langauge needs Tensor Loops for Time and Space maximization
*  Decoding Cost of MOV
   + Reg < Cache <  Buffer < DRAM 
   + Large Register size better
   + Double Buffering is good
   + RAM technology. SRAM, DRAM, HBM
   + Buffer Specialization, Read only buffers and write only buffer perform better than R/W
   + Quantifiable Design Concept
  

### Co-designing PE-MEM
-  For Typical Operation, 2x DDR MOV RD to Reg, 1 MAC, 1x DDR MOV WR from Reg
-  Considering NN Type, 
   + Calculate Order of Execution and Data Memory Access Patterns 
     - idea check : Why Build Simulator that identifies Patterns of Data movements!!!
   + For Accumlation PE Pattern,  ADDER Tree implementation and SYSTOLIC accumlation for matrices
   + For Non-Accumation Pattern, Direct Wiring multicast and SYSTOLIC multicast for matrices

   + Keep Stationary Data in closest register,without movements if possible
     - idea check : Register Allocation feedback based based on Spill factor and Movement factor.
     - Move Input into Register, Keep Weights stationary or  Keep Partial Sums and Output Stationary.
    + Scale MAC and LOCAL Memory up.
	

### Design parameter : ISA Decoder
 + CPU/GPU Decoders are complex for ML Ops.inefficiency into  Pipelining Operations <D-cache> CKT
 + Short Decoder sequences better, small, efficient for only Unit Ops.
 + SIMD decoder <> VLIW decoder ckt
	
### Design Parameter : Compiler Support
+ Input (Tensor) Slicing at programming lang and backend level 
+ Re-organize Loop order, polyhydrals for better memory movements, split loops into more loops.
+ Optimize for Area x Energy 
	
### Design Paramter : Choice of Hardware transistor tech 		
 * High Frequency, high throughput results in heating
 * Low frequency, high thougput, optimal area serves better.
 * Diaelectric Silicon technology in nm 
 * Photonic Technology
 * Calculate Compute Density , Compute to data movement ratio to classify IO dominant or Compute Dominant
	

