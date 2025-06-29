---
layout: post
title: "[Notes] LLVM Machine Scheduler " 
---

* ScheduleDAGRList
 	- Legacy Scheduler
 	- Selection DAG -> Liner order of MI
 	- Has Heuristics
* MachineScheduler
	- New Scheduler
	- Model Resources usage per instruction
	- Uses  ScheduleDAGRList with source order heuristics

	- PRE-RA  & PostRA 
	- Strategy
		- Top, Bottom, Bidire
		- GenericScheduler::TryCandidate

	- ScheduleDAGMI - Driver for list scheduling
	   + Builds graph
	   + Runs List Scheduling - Requests new Node from "Scheduler" & TargetScheduleModel

* Customizing 
	+ custom new Scheduling Policy
		Subtarget::overrideSchedPolicy(MachineSchedPolicy)
	+ custom MachineSchedStrategy Interface Implementation
		- ScheduleDAGMI uses it
		- pickNode() -- Entry Point
		- schedNode()
	+ OR Extend GenericScheduler for re-using 
	+ DAG mutations
		- Scheduling heuristics drawbacks can be covered
		-  Allow Scheduling Constraints e.g. Weak Edges and Ajust dependencies using target info
		- but reduces flexibility
		- some ISA is fast if back to back, CMP & JMP
		- e.g. MacroFusion

	+ Challenges Increase register pressure
* SchedMachineModel
	+ Each Target defines Operand Category (SchedReadWrite)

	Types 
	+  class SchedReadWrite - Instruction Operand Type
	+  class SchedWrite : SchedReadWrite - Def Opd
	+  class SchedRead  : SchedReadWrite - Use Opd

	+ Operand Category
	def WriteADD32 : SchedWrite

	+ Each Cateogry with Instruction
	def ADD: X86Inst< > , Sched<WriteADD32>{}

	+ Each Subtargt defines pipeline and resources
	def  ALUUnit2 : ProcRsource<1> { let bufferSize = 0}

	+ Associate categories with resources and latencies
	def WriteRes<WriteADD32,[ALUUnit2]> {let latency = 3;}

	+ Pipeline Forwarding
	+ Subtarget level override for ISA 
		InstRW<>
	+ Partially Pipelining
		Resource Cycles - How long Op hogs resource
	+ Predicates
	+ SchedVariants


Commands:
>>> clang -mllvm -enable-misched -mllvm -enable-post-misched -mllvm -misched-postra

>>> llvm-tablgen --debug-only=subtarget-emitter --print-records -I=////

>>> llc -enable-misched -debug-only=machine-scheduler

Reference:
llvm/include/llvm/Target/TargetSchedule.td
llvm/lib/Target/X86/X86Schedule.td
llvm/include/llvm/MC/MCSchedule.h

https://www.youtube.com/watch?v=brpomKUynEA
class Diagram
https://llvm.org/devmtg/2016-09/slides/Absar-SchedulingInOrder.pdf
https://www.youtube.com/watch?v=kjufVhyuV_A&ab_channel=AhmadYasin
https://sites.google.com/site/analysismethods/yasin-pubs
https://github.com/andikleen/pmu-tools/wiki/toplev-manual