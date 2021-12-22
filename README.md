## 2021.12.9
### Paper: Loop Parallelization in the Polytope Model<br>
### Author: Lengauer,Christian. <br> <br> Publish: International Conference on Concurrency Theory. Springer, Berlin, Heidelberg, 1993
#### Content:
1) The paper introduced a model called polytope which can present perfect nested loops.<br>
2) By using this model, the given loop can be transformed to the targeted interpretation.<br>
3) It uses an example to show how to build a polytope model, transfrom it to a target polytope and at last transform the target polytope back to for-loop.<br>

### Paper: HIGH PERFORMANCE CODE GENERATION IN MLIR: AN EARLY CASE STUDY WITH GEMM (Need re-reading)<br>
### Author: Uday Bondhugula. <br> <br> Publish: arXiv:2003.00532 2020
#### Content:
1) This paper showcased how MLIR works by using matrix multiplication(2088*2048*2048) as its benchmark.<br>
2) The author used the tiling model proposed by "BLIS: A Framework for Rapidly Instantiating BLAS Functionality"
3) Many techniques has been included in this paper, and the author presented the result step by step, finally achieved 92% peak performance.<br>
4) Techniques: Tiling, memory packing(making data contiguous in mem),unroll, vectorization(operands vectorization),tuning tiling parameter to fit vector register.<br>

## 2021.12.10
### Paper: ScaleHLS: A New Scalable High-Level Synthesis Framework on Multi-Level Intermediate Representation<br>
### Author: Hanchen Ye, Deming Chen. <br> <br> Publish: arXiv:2107.11673
#### Content:
1) This paper proposed an end-to-end framework on MLIR to optimize particular algorithm.<br>
##### Representation:<br>
2) HLS C will be translate to SCF(structured control flow) by using HLS C front-end(clang).Framework like ONNX and Pytorch will first be translate to onnx and torch dialect respectively through thrid party mlir front-ends(Gragh level).<br>
3) Then GL IR will be translate to Loop -level IR(affine and SCF) by using MLIR.<br>
4) "HLSCPP" is designed as a novel dialec to present the HLS-structure into Directive-level.<br>
![image](https://user-images.githubusercontent.com/77606152/145691926-b4b8f233-7dc3-4940-a9a2-21e28d364d5e.png) <br>
##### Optimization:<br>
![image](https://user-images.githubusercontent.com/77606152/145692197-4bd5f017-92d5-4618-9a16-f9296ad8dfb1.png)<br>
5) Each level has multiply passes(optimization methods).<br>
6) Graph level:①legalize ②Split function(Not understand yet)<br>
7) Loop level:①perfectization(Change to perfect loop) ②loop-order(data locality) ③remove variable-bound(change to recutangular loop) ④loop-tile(data locality) ⑤unroll.<br>
8) Direct:①function and loop pipelining ②array-partition<br>
9) Others:e.g. ①affine if(eliminate dead branch)  ②store-forwarding<br>
##### Design space optimization:<br>
10) QOR+DSE engine + Pareto frontier: In order to search the optimal solution under resource, latency and throughput constraint.:<br>
##### Experiment:<br>
11) Baseline:Polybench-C.<br>
12) Benchmark:Kernels and networks.<br>
13) Result: Good performance improvement, good scalability(problem size), good runtime.

## 2021.12.17
### Paper:RASA: Efficient Register-Aware Systolic Array Matrix Engine for CPU
### Author:Geonhwa Jeong, Tushar Krishna. <br> <br> Publish: DAC 2021
#### Motivation:<br>
1) Despite the advantage of GPU and FPGA that have widely being studied, most DNN tasks are still done in CPU due to its programmability.<br>
2) GEMMs are widely used in many DNNs, like FC in CNN, BERT and DLRM. Convolutional layers can also be lowered to GEMM.<br>
3) Systolic array enables gaining high computation performance while maintain good energy efficiency, because of its high concurrency and compute to communication ratio.<br>
4) However, no pervious work explore the design trade-off of integrating systolic-array in CPU for GEMMs.<br>
#### Challenge: 
Under-utilization in systolic array.<br>
Reasons:<br>
1) Mapping inefficiency lead to PE idles.<br>
2) Memory bound.
3) Pipeline fill and drain delay. (Focus on this one)<br>
#### Possible solutions:
1) Using richer interconnects to reduce the fill and drain time. However, expensive in area and power cost.<br>
2) Using large tile size. However, the register in CPU are limited.<br>
#### Solution proposed:
1) Instruction overlap<br>
![image](https://user-images.githubusercontent.com/77606152/146601711-557f66d7-0af2-421e-b5a4-2831436df612.png)<br>
#### Experiment:
1) Platform: Macsim(simulaor) CPU and NOC runs at 2GHz. Systolic array 500MHz<br>
2) Benchmark: ResNet50, DLRM, DLRM<br>
3) Result: RASA-DMDB-WLS(Double multiplication, double buffer and Weight load bypass) gains 79.2% compared with baseline.<br>
![image](https://user-images.githubusercontent.com/77606152/146602334-f0ea5844-9cea-4fd6-8954-0d353be065f8.png)
4) Performance Per Area: Implement it using RTL, runs DC compiler on Nangate 15nm. Compared with Intel Skylake GT2
4C CPU, it cost 0.7% of the total die size. <br>
![image](https://user-images.githubusercontent.com/77606152/146602544-1478ea0b-f9ea-40c4-9656-771acca2677c.png)
#### Criticize:
1) How do they decide the data flow.(WS are used in the design without analysis)<br>
2) How does it behave when using different data type(BF16 in, FP32 out). Generality need to be shown.<br>

## 2021.12.18
### Paper:Time-Predictable Acceleration of Deep Neural Networks on FPGA SoC Platforms
### Author:Francesco Restuccia and Alessandro Biondi. <br> <br> Publish: 2021 IEEE Real-Time Systems Symposium
#### Motivation:
1) Thanks to the cycle accurate property of FPGA, many Cyber-Physical Systems(safety-critial applications) can be done on FPGA.<br>
2) FPGA is not amiable to software programmers. Vitis AI supports mainstream frameworks by using DPU core.<br>
3) Thus it is important to figure out the behaviour of DPU.<br>
#### Contributions:
1) Designed hardware profiler to profiling the execution behaviour of DPU.<br>
2) Based on the profiling data and execution model, they formed a pessimistic analytical model for predicting the run time.<br>
3) Proposed DICTAT, by redirecting the ins to PS on chip memory, it can amortize the burden of fetching ins and data both on DRAM.<br>
#### Challenge:
1) The behaviour of DPU parallism(Read, write, Ins fetch) and DRAM can not be measured accurately.<br>
#### Experiment result:
1) The run time model is not accurate, but gives us a pessimistic results.<br>
![image](https://user-images.githubusercontent.com/77606152/146655280-9a4800ea-41d5-41e9-b022-dc8ac1d950d6.png)
#### Defects:
1) DPU model not accurate.<br>
2) DDR behaviour don't know.<br>
3) Don't consider PS interupt.(Adding this factor to pessimistic estimation of DRAM)<br>
4) Lack of generality. Only target on Zynq Ultrascale.<br>

## 2021.12.22 (CNN Part need to be read again)
### Paper:Search for Optimal Systolic Arrays: A Comprehensive Automated Exploration Framework and Lessons Learned
### Author:Jie Wang and Jason Cong. <br> <br> Publish: arXiv:2111.14252v1
#### Motivation:
1) Incomplete design space.(tiling factor selection, divisor)<br>
2) Inaccurate performance modeling.(Given a settled design the estimated HW utilization and throughput is not as closed as the real one)<br>
3) Inefficient serach methods.(Targeting on part of the objection, e.g. of-chip data communication only)<br>
#### Contrubitions:
1) Comprehensice design space and accurate performace modeling. (Dataflow, loop permutaion and tiling)<br>
2) Mathematical Programming based optimization and evolutionary search.(First use MP to provide a relative better model then use evolutionary algorithm to search)<br>
#### Experiment/conclusion(CNN part need to be added):
1) Performance Model Validation<br>
   a. Platform: U250<br>
   b. Baseline: HLS report<br>
   c. Benchmark: MM[64,64,64] CNN[16(Inc),16(Outc),16,16,3,3] <br>
   d. Result: Error rate, 1.99%, 0%, 0% for latency, BRAM and DSP <br>
2) Auto-tuning Evaluation<br>
   a. Platform: Intel Xeon E5-2680 v4 CPU<br>
   b. Baseline: 18 other search methods.<br>
   c. Benchmark: MM[1024,1024,1024]<br>
   d. Result:Odyssey locates designs that achieve more than 95% of the optimal performance<br>
   ![image](https://user-images.githubusercontent.com/77606152/147143570-2e435415-2299-4c13-9d9a-cc4503f3ae3d.png)
3) Application MM[1024,1024,1024]<br>
   Dataflow:<i,j>  Loop permutation:<[i,j],k> <br>
   ![image](https://user-images.githubusercontent.com/77606152/147143692-86c9e16e-b5e7-4f25-b538-0805d791df0e.png)
   ![image](https://user-images.githubusercontent.com/77606152/147143931-8533c155-d463-4bf3-81ea-c90713a57c22.png)
