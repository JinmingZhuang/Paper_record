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
6) Graph level:???legalize ???Split function(Not understand yet)<br>
7) Loop level:???perfectization(Change to perfect loop) ???loop-order(data locality) ???remove variable-bound(change to recutangular loop) ???loop-tile(data locality) ???unroll.<br>
8) Direct:???function and loop pipelining ???array-partition<br>
9) Others:e.g. ???affine if(eliminate dead branch)  ???store-forwarding<br>
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

## 2021.12.22 
### Paper:Search for Optimal Systolic Arrays: A Comprehensive Automated Exploration Framework and Lessons Learned (CNN Part need to be read again)
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

## 2021.12.24
### Paper: CoSA:Scheduling by Constrained Optimization for Spatial Accelerators(Re-reading needed)
### Author:Yakun Sophia Shao. <br> <br> Publish: 2021 ISCA
#### Motivation:
1) Three main schelduers have their drawbacks. <br>
   A. Brute-force: Expensive cost on exhaustive search for complex accelerators. <br>
   B. Feedback-driven: can't be used in hardware development period. <br>
   C. Constrained-optimization: Lack direct support for tile-size(Mannual enter<br>
#### Content:
1) Target on DNNs with 7 variables on spatial architecures.<br>
2) Scheduling decisions: Loop tile, loop permutation, sptial mapping.<br>
3) CoSA model build: Buffer, computational resources constrained. <br>
4) Objection: Maximum on-chip buffer utilization, mimimize computational latency, minimize data traffic. Overall consideration, give each objection a weight.<br>
#### Experiment:
1) Platform: Time loop for performance and energy estimation. NoC simulator(More sensitive for data communication).<br>
2) Baseline Scheduler: Time loop mapper and random schedular(stop when finding five feasible solution).<br>
3) Result:<br>
   A. Search time speedup on Time loop 5.2X and 1.5X<br>
   ![image](https://user-images.githubusercontent.com/77606152/147369094-5285b7d4-e706-4c15-9706-eb070f2ee9f9.png)<br>
   B. Search time speedup on NoC 3.3X and 2.5X<br>
   ![image](https://user-images.githubusercontent.com/77606152/147369225-ae188770-d0be-4c4d-bda1-868062184b7e.png)<br>
   C. Energy on Time loop 3.3X and 1.22X<br>
   ![image](https://user-images.githubusercontent.com/77606152/147369275-c21f507f-910f-45dc-9c06-c855247cd696.png)<br>
#### Argument:
1) Didn't show the result of objective function acheived by these three schdulers.<br>
2) Didn't consider DNN sparsity<br>

## 2021.12.26
### Paper: Why Systolic Architectures? 
### Author:H.T. Kung. <br> <br> Publish: 1982 Computer.
#### Motivation:
1) Solve the I/O bandwidth bottleneck in compute-bound applications.<br>
#### Principle:
1) Single access from mem, multiple use in PEs.<br>
#### Content:
By using 1-D convolution as an example to illustrate different systolic array design method.<br>
A. With global data communication<br>
1) Broadcast input , move results , weights stay.<br>
![image](https://user-images.githubusercontent.com/77606152/147392030-797dc768-ca3f-4379-9701-8dfc2ea2e33f.png)<br>
2) Broadcast input , move weights , results stay.<br>
![image](https://user-images.githubusercontent.com/77606152/147392058-3e063017-cd98-4ee4-8ce7-07de683afa31.png)
3) Fan-in results, move inputs, weights stay<br>
![image](https://user-images.githubusercontent.com/77606152/147392074-9598326f-d0b6-4a0b-a427-ba0ea00730a6.png)
Conclusion: <br>
For design 1, the width of y may be bigger, however, it doesn't need any output path. For design 2, it can improve precision with effcient cost. For design 3, control logic straight forward, however if k is large it need many levels of adder tree.<br> <br>

B. Without global data communication<br>
1) Results stay, inputs and weights move in opposite directions.<br>
![image](https://user-images.githubusercontent.com/77606152/147392150-825b093e-59ce-43b4-894c-38a97bdb53e1.png)
2) Results stay , inputs and weights move in the same direction but at different speeds.<br>
![image](https://user-images.githubusercontent.com/77606152/147392160-816a9c43-cb04-4077-9fda-f8b3e0d2d1b3.png)
3) Weights stay, inputs and reslults move from opposite direction.<br>
![image](https://user-images.githubusercontent.com/77606152/147392195-99a44f89-cd36-46d9-a07d-7eee97fb0791.png)
4) Weights stay, inputs and reslults move from same direction, but with different speed.<br>
![image](https://user-images.githubusercontent.com/77606152/147392205-30e855e0-2e89-468f-ae51-1e7bfc2e0439.png)
Conclusion:<br>
For design 1 and 2, extra output path is needed. 1 only achieves half computational efficiency.<br>
#### Advantage of systolic array:
1) Make multiple use of each input data.<br>
2) Leverage compute concurrency. (Pipeline compute in many stages or multiprocess many result simultaneously)<br>
3) Simple cell and simple data and contriol flow<br>

## 2021.12.27
### Paper: SuSy: A Programming Model for Productive Construction of High-Performance Systolic Arrays on FPGAs
### Author:Yi-Hsiang Lai and Zhiru Zhang. <br> <br> Publish: 2020 ICCAD
#### Motivation:
   HLS users often have o perform micro-coding whichc need mch knowledge of low level hardware architectrue. And woould require a lot of manual effort to design a high performace systolic array design.<br>
#### Contribution:
1) A clean model is proposed, which seperate the temproal definition and spatial mapping.<br>
2) An explict representation of space-time transformation is provided.<br>
3) Many spatial optimization tehniques are provided, including vectorization, reuse buffer, data gatering and scattering for I/O. <br>
4) Completed design flow.<br>
#### Experiment:
1) Platform: Intel Arria 10GX and Xilinx UV9P.<br>
2) Baseline: Other works and the work only has space-loop transformation but without techniques.<br>
3) Benchmark: GEMM (16bits)
4) Result:<br>
   A. Hardware utilization and performance improvement for each techique<br>
   ![image](https://user-images.githubusercontent.com/77606152/147525804-5961d93c-5862-425a-bb38-c37a2d37419a.png)<br>
   B. Comparison<br>
   ![image](https://user-images.githubusercontent.com/77606152/147525829-7d69c26b-00ae-4097-8875-e84b046027d6.png)<br>
#### Argument:
1) Didn't discuss the method of deciding best choice(No analytic model, no any LP).<br>
2) Didn't have much performance improvement.

## 2021.12.28
### Paper: PolySA: Polyhedral-Based Systolic Array Auto-Compilation Systolic Arrays on FPGAs
### Author: Jason Cong and Jie Wang. <br> <br> Publish: 2018 ICCAD
#### Motivation:
   HLS users often have o perform micro-coding whichc need mch knowledge of low level hardware architectrue. And woould require a lot of manual effort to design a high performace systolic array design.<br>
#### Contribution:
1) Built an end to end compilation framework for generating high performance systolic array.<br>
2) Proposed a polyhedron framework based transformation front-end.<br>
3) Used HLS as back-end. <br>
4) Built an analytic model for choose optimal solution
#### Experiment:
1) Platform: Intel Xeon E7-4907CPU and Xilinx UV9P.<br>
2) Baseline: Manually designed MM and CNN.<br>
3) Benchmark: GEMM (16bits) 1024*1024*1024 and the third layer of VGG-16
4) Result:<br>
   A. Hardware utilization and performance comparison<br>
   ![image](https://user-images.githubusercontent.com/77606152/147610284-6f8136dc-884c-481c-abcb-c813c850a09e.png)<br>
   B. Analytic model error rate within 10%<br>
   ![image](https://user-images.githubusercontent.com/77606152/147610368-acd0879c-5db7-4002-bcf1-d04205a2382b.png)<br>
   C.Runtime result
   ![image](https://user-images.githubusercontent.com/77606152/147610393-34c610b0-1993-42e2-8752-3377095e90f1.png)<br>
#### Argument:
1) 31% and 9% performance gap between automatical generated code and manually design of MM and CNN respectively<br>
2) Didn't use any optimization for polyhedron transformation.
3) Use iteratively exploring which lacks of scalability.

## 2022.1.3
### Paper: Timeloop: A Systematic Approach to DNN Accelerator Evaluation
### Author: Angshuman Parashar. <br> <br> Publish: 2019 ISPASS
#### Motivation:
   The large design space and lack of a systematic approach to evaluate the perfomance and energy efficiency of an accelerator make DNN accelerator design tend to be more of an art. Timeloop provides a evaluation tool (mapper + model) to evaluate different accelerators and guide deisgners to optimize their design.
#### Contribution:
1) Proposed a concise and unified way if describing the architecture of hardware and DNN structure.<br>
2) Combined the exploration of a large design space witha mapper.<br>
3) Provide a tool for fair comparsion among different accelerators. <br>
4) Use exhaustive search (small design space) and heuristic (large design space) to find an optimal mapping solution.<br>
#### Content:
1) Mapper: Can do optimization under some constraint(dataflow, tiling, loop permutation, loop parallelism).<br>
2) Model: 
A. Count the access number of off-chip and on-chip access by using delta(set difference between consecutive iterations), for spatial loop it determines the data transfering pattern(Multicase or forwarding ...). For time loop, it determines data reuse.<br>
B. Use TSMC 16nm model to calculate energy.
#### Experiment:
1) Accelerators: NVDLA-derived(Similar to NVDLA. sane dataflow and with spatial reduction) and Eyeriss.<br>
2) Baseline: NVDLA-derived RTL design and Eyeris report.<br>
3) Benchmark: 107 DNN workloads in DeepBench
4) Result:<br>
   A. Energy validation results for NVDLA-derived architecture<br>
   ![image](https://user-images.githubusercontent.com/77606152/148003863-9593d683-1db8-4cbf-aea6-745f2c6f101d.png)<br>
   B. Performance validation results for NVDLA-derived architecture<br>
   ![image](https://user-images.githubusercontent.com/77606152/148004095-89e2e694-d87e-46b1-b5d6-0c7a632abc74.png)<br>
   C.Normalized energy for AlexNet layers on a 256-PE Eyeriss architecture employing a row-stationary dataflow
   ![image](https://user-images.githubusercontent.com/77606152/148004117-d5186bed-c892-45f3-9b74-1f127fb20421.png)<br>
#### Argument:
1) Need programmers intervene<br>
2) The mapping algorithm is not good enough, for small size use exhaustive search, for big space using hurestic.
3) Loop optimization limitation, loop tiling can only be factors of outer loop.

## 2022.1.21
### Paper: AutoSA: A Polyhedral Compiler for High-Performance Systolic Arrays on FPGA
### Author: Jie Wang, Licheng Guo and Jason Cong <br> <br> Publish: 2021 FPGA
#### Motivation:
1) Design systolic array require both high-level characteristics of the application andd hardware backgroud, thus makes it hard to be implemented.<br>
2) Previous automatic synthesis work suffers from limited generality, performance and productivity.<br>
#### Contribution:
1) Provide an end-to-end compilation flow to generate systolic array from C to HLS C.<br>
2) Proposed polyhedron based algotithm to do loop optimization.<br>
3) Case study: High performance kernel are generated by using AutoSA.(fp32 934GFLOPs, int16 3.41TOPs, and int8 6.95TOPs) <br>
#### Content:
1) Computation :Polyhedron model. Space-time transformation: Array-partitioning, Latency hiding, SIMD.<br>
2) Communication: Based on data dependency, deciding I/O network to send data.
3) Auto_tuning: Sapmle 16 eample to build resource model and use loop count to model latency. Then exhaustively search all the possible solution.<br>
#### Experiment:
1) Platform: xilinx U250,300MHz(FP32,INT8), 250MHz(int16).<br>
2) Baseline: PolySA, Susy et.cl.<br>
3) Benchmark: MM, LU Decpmposition, CNN, MTTKRP,TTMc
4) Result:<br>
   A. Three case for MM<br>
   1???Time-space and I/O overview
   ![image](https://user-images.githubusercontent.com/77606152/150580563-3035bcf4-d351-4ed9-96a6-77e2273b92fa.png)<br>
   2???Hardware resources
   ![image](https://user-images.githubusercontent.com/77606152/150580755-c546daaf-49eb-4e81-9508-3a5003e13808.png)<br>
   3???Performance comparison
   ![image](https://user-images.githubusercontent.com/77606152/150580826-3c22e6f6-7695-411b-8fda-697653dcb724.png)<br>
   B. LU decomposition<br>
   1???Time-space and I/O overview
   ![image](https://user-images.githubusercontent.com/77606152/150580971-73d86bc0-cf5c-4d30-842b-2351598bf235.png)<br>
   2???Performance comparison
   ![image](https://user-images.githubusercontent.com/77606152/150581103-198651b2-0f16-4742-8c13-3e2fe417dbdf.png)<br>
   C.Other benchmarks<br>
   ![image](https://user-images.githubusercontent.com/77606152/150581202-6c3d7ae6-2f5f-44e2-9310-64c9ab353db1.png)<br>
#### Argument:
1) The auto-tuner lacks of scalability since it uses exhaustive search.
2) There are some restriction on their SIMD technique. Need to be Prallel loop or reduction loop which is 1D SIMD??? not suitble for some 2D SIMD in Versal ACAP. 

## 2022.1.24 (Reread)
### Paper: Loop Parallelization in the Polytope Model<br>
### Author: Lengauer,Christian. <br> <br> Publish: International Conference on Concurrency Theory. Springer, Berlin, Heidelberg, 1993
#### Content:
1) The paper introduced a model called polytope which can present perfect nested loops.<br>
2) By using this model, the given loop can be transformed to the targeted interpretation.<br>
3) It uses an example to show how to build a polytope model, transfrom it to a target polytope and at last transform the target polytope back to for-loop.<br>
#### Detail process:
1) Algorithm should be presented in SSA(Static single assignment) form.<br>
2) Index space are presented as A*x <=b, in which A is the calulated from the lower and upper bound, b is the loop bound restriction, x is in element in index space.<br>
3) We need to get the funtion t(x) and a(x) for new timeline and allocation. t(x) should keep the data dependency, and a(x) determine the paralleilism by keeping data dependency.<br> 
![image](https://user-images.githubusercontent.com/77606152/150886351-7b65df53-b3cd-4cc6-80b0-ce73005fefd4.png)<br>
4) T=[??;??];In the full dimensional case(space loop takes r-1 dimension and temproal loop takes single loop), T should be a squre matrix.<br>
5) For Target space since Tx=y, AT(-1)y<=b, thus loop boundary gotten.<br>
6) Target program: replace old index with new index.<br>
#### To do list:
1) Figure out how to get feasible t(x) and a(x) with specific objective function, either minimum exe time or processor number or memory usage.
2) How to present systolic array in SSA format and use polyhedral model to solve it.

## 2022.1.25
### Paper: Multimodal Neural Network Acceleration on a Hybrid CPU-FPGA Architecture: A Case Study<br>
### Author: M. T. Ajili and Y. Hara-Azum. <br> <br> Publish: IEEE Access 2022
#### Issue/Motivation:
Although DeepSense (a multimodal NN consists of CNN and RNN) is designed for low-power mobile and IoT devices, it is still unable to meet these devices' power requirements. Thus they aim to use FPGA SOC(YNQ, U96 and ZCU102) to achieve high energy efficiency.<br>
#### Content:
1) Showcase the process of building a custimized hw/sw co-design based on xilinx-VI library and DPUs.<br>
Phase one: Train their own modified DeepSense NN to meet the requirement of DPU.<br>
Phase two: Explore the DPU parameter settings based on their application and set up the platform via Vivado and Vitis.<br>
Phase three: Use Vitis AI quantizer and compiler to generate the DPU instructions based on the DPU parameter. Manunally implement unsuported layer in host side.<br>
2) Exploring different settings on two B512 DPU to see the influence on latency, utilization and power consumpssion.<br>
#### Result:
1) Platform: Xilinx ZYNQ U96 + ZCU102 + Raspberry Pi 3 model B.<br>
2) Benchmark: Deepsense
3) Baseline: Oringial paper which implemented on Nexus 5 with 2.3GHz CPU.<br>
4) Result:<br>
A.Latency(1 batch): U96 2.5X, ZCU 2.9X<br>
![image](https://user-images.githubusercontent.com/77606152/151075636-e0a488ae-0cf6-40ff-bfa8-b49ef3371cd8.png)<br>
B.Energy consumption per inference: U96 5.2X, ZCU 4.6X<br>
![image](https://user-images.githubusercontent.com/77606152/151075825-7e229540-c1a9-4d52-85af-ee40fb43d092.png)<br>
4)Insight:<br>
Low Power Mode should be used which can gate off DPU when in idle cycles without consuming any resource.<br>

## 2022.1.27
### Paper: Halide: A Language and Compiler for Optimizing Parallelism, Locality, and Recomputation in Image Processing Pipelines<br>
### Author: Jonathan Ragan-Kelley and et.cl. <br> <br> Publish: PLDI'13<br>
#### Issue/Motivation:<br>
How to automatically design a schedule that have good parallelism, locality and low recomputation for stencil applications<br>
#### Content:<br>
1) Built a systematic model of the tradeoffs between Parallelism, Locality, and Recomputation.<br>
2) An algorithm scheduling seperated representation is proposed. 
3) Compile Process: <br>
A. Autotuner will stochastically search the space of valid schedules to find a high performance implementation.<br>
B. Lowering, bound inference: Transform schedule and algorithm to for-loop.<br>
C. Sliding window optimization: Storage reuse between higher loop and lower loop.<br>
D. Flatten: Flatten multidiension load/store to 1d.<br>
E. Vectorization and Unroll.<br>
F. Backend: Use LLVM for low-level code generation.<br>
![image](https://user-images.githubusercontent.com/77606152/151494264-375b4fba-cdc5-4303-bf0a-c6fb4a5ed37a.png)<br>
#### Result:
1) Baseline: Expert tuned C code compilerd by GCC 4.7.<br>
2) Platform: Xeon W3520 X86 CPU and Tesla C2070 GPU.<br>
3) Benchmark: Blur, Bilateral grid, Camera pipeline, et.cl<br>
4) Result:<br>
A. Latency x86<br>
![image](https://user-images.githubusercontent.com/77606152/151495644-37c94a84-d7b8-4054-af4f-dea10e80585c.png)<br>
B. Latency GPU<br>
![image](https://user-images.githubusercontent.com/77606152/151495723-a88b06c0-6b57-4a62-94a8-c6a17e4b773d.png)<br>

## 2022.1.29
### Paper: SODA: Stencil with Optimized Dataflow Architecture<br>
### Author: Yuze Chi, Jason Cong, Peng Wei, Peipei Zhou. <br> Publish: ICCAD'18<br>
#### Issue/Motivation:
Find optimal solution for stencil applications and automatically implement it. <br>
#### Content:
1) Presented the microarchitecture and methodology for stencil application.<br>
A. Achieved minimum off-chip data transfer, since data is only loaded once.<br>
B. Under certain unroll factor K or PE number, minimum on-chip buffer is used.<br>
2) Performance and resource utilization model is built for design exploration.???Given unroll factor K, find maximum throughput???<br>
3) Automatic framework: <br>
A. Only SODA DSL kernel is needed. <br>
![image](https://user-images.githubusercontent.com/77606152/151687168-657baa4c-4ade-4471-9bf2-a7dc5216d5a0.png)<br>
#### Result:
1) Baseline: CPU(Use Halide), other framework.<br>
2) Platform: XCKU060 FPGA, Intrel Xeon E5-2620 v3 CPs.<br>
3) Benchmark: SOBEL 2D, DENOISE 2D, DENOISE 3D, JACOBI 2D and et.cl
4) Result:<br>
A. Model Accuracy<br>
![image](https://user-images.githubusercontent.com/77606152/151687263-94474d5d-40b8-486c-bc87-636e9ea95ac4.png)<br>
B. Throughput<br>
![image](https://user-images.githubusercontent.com/77606152/151687275-b65ee053-7b3b-48f7-a08e-0fb34d6adf7d.png)<br>
#### Argument:
1) Design space exploration is not complete, since k is given by user. What if user want to achieve maximized throughput on a specific platform. How to define K?<br>

## 2022.1.29
### Paper: Domain Specific Hardware Accelerators<br>
### Author: BY WILLIAM J. DALLY, YATISH TURAKHIA, AND SONG HAN. <br> Publish: COMMUNICATIONS OF THE ACM'20<br>
#### Content:<br>
1) Why DSAs?<br>
Moore???s Law and Dennard scaling have largely ended, which slows the scaling of performance and efficiency of processors.<br>
2) How DSAs help?<br>
A. Specialization: Specialized logic takes minimum cycles. Specialized data type helps locality, communication and performace.<br>
B. Parallelism: By specializing the application to a given domain, the synthronization and communication between PEs are greatly simplified, which makes parallelism much easier.<br>
C. Local and optimized memory: The area and power are both dominated by memory. Optimized memory structure and scheduler helps.<br>
#### Guidance:
1) Software and hardware codesign: Algorithm should be hardware friendly, for example, Deepcompression achieves 30x model reduce however the overhead of sparse method makes these algorithm uninteresting except for memory compression.<br>
2) Automation is needed: Flexible and programmable DSL can make DSA an excellent ecosystem. <br>

## 2022.2.1
### Paper: LLVM: A Compilation Framework for Lifelong Program Analysis & Transformation<br>
### Author: Chris Lattner and Vikram Adve. <br> Publish: CGO'2004
#### Content:
1) An Novel Code Representation<br>
A. A low level language-independent type system: Every SSA register and explicit memory object has an associated type, and all operations obey strict type rules. Besides, It supports a broad class of high-level informations on low-level code and provides data and operation implementation behaviours for all the stage optimization.<br>
B. Explicit Memory Allocation: malloc, free and alloca are provided for explicit memory allocation.<br>
C. Explicit low-level machine independent function call and exception handling mechanism is proposed.<br>
2) A Compiler Design<br>
A. Persistent program information:LLVM IR(Exsist during the life time of the program).<br>
B. Offline code generation.<br>
C. Combine Runtime and offline optimization: Profiling information at run-time in the field so that it is representative of actual users, enabling profile-guided transformations both at run-time and in idle time off-line.<br>
D. Transparent runtime model: Any language can be compiled by it.<br>
![image](https://user-images.githubusercontent.com/77606152/152232579-426ea8b9-1b6f-4b82-ac1e-da3c518bbaeb.png)<br>
#### Experiment Result:
1) Platform: Intel Xeon processor.<br>
2) Speed compared with GCC3.3.(DGE,DAE and inline are interprocedural optimizations work on the whole program at link-time)<br>
![image](https://user-images.githubusercontent.com/77606152/152233948-ac5168fb-2e54-4f4c-ac60-b30a68eaca30.png)
#### Why LLVM or What's the advantage:
1) It compiles the source code to an intermediate representation, thus a given language can be compiled to LLVM and do optimization in this level regardless of backend.<br>
2) Since there's a uniformed IR, the different languages can work on same back-end.<br>
![image](https://user-images.githubusercontent.com/77606152/152234902-e9ca8193-4f5e-44a4-be05-2ec1942cff7e.png)

## 2022.2.3
### Paper: A singular loop transformation framework based on non-singular matrices<br>
### Author: Li, Wei, and Keshav Pingali. <br> Publish: International Workshop on Languages and Compilers for Parallel Computing. 1992<br>
#### Content:
1) Overview: Introduced a complete procedure for space-time transformation by using linear representation.<br>
2) Procedure: <br>
A. Loop transformation:<br> 
Use vector to present loop space: <br>
If the iteration space of Snew is sparse, a non-singular T can always be decomposed to H and U where H is a lower triangular matrix with positive diagonal elements and U is a unimodular matrix U. U * Sold is always a dense space which keeps the lexicographical order.<br>
```sh
Sold = [ 0, 1 ]    in which [ 0 ]  represents j loop and [ 1 ] represents i loop   
       [ 1, 0 ]             [ 1 ]                        [ 0 ]
D    = [ 3, 2 ]^T  refers to the dependency
Sold * T = Snew
T * D >=0, at least one of the row is greater than 0
for i=1:3
  for j=1:3
    A[i,j]=A[i-3,j-2];
```
B. How to decide T:<br>
Decide what is the objection. For example, if we want to unroll the outer loop, let P=T(1,:) = (2 , -3), thus T * D (1,:) = 0, which means there's no dependency anymore for outer loop. To decide the next row of T, we use ek= (1,0) and do a projection to make sure its independent with the previous decided row in T that is (2, -3). The new row should be y= Q * (ek) where Q =(I - P(P^TP)^(-1)P^T)<br>

## 2022.2.4
### Paper: HeteroHalide: From image processing DSL to efficient FPGA acceleration<br>
### Author: Li, Jiajie, Yuze Chi, and Jason Cong. <br> Publish: FPGA'2020<br>
#### Motivation:
Exsisting framework from Halide to FPGA is lack of portability (Only supports  xilinx ZYNQ FPGA) and maintainability (specific microarchitecture).<br>
#### Content:
1) Built an easy-to-use compile flow from Halide to FPGA.<br>
2) Use HeteroCL as IR, SODA, PolySA and Merlin Compiler as backend.<br>
3) Made some extensions on Halide Schedules. Based on schedule, Halide will generate IR directly. However, to generate efficient accelerators on FPGA, some schedules  should be used in HeteroCL level, thus extensions  are needed.<br>
#### Experiment:
1) Platform: CPU: Xeon 2680v4 x2, 28 cores, 14nm, f=2.4GHz; Xilinx Vu9P: 16nm, f=250MHz; Xilinx ZYNQ7020.<br>
2) Benchmark: Harris, Gaussian, GEMM, K-Means et.cl<br>
3) Result:<br>
A. Productivity: Lines of code compared with xfOpenCV<br>
![image](https://user-images.githubusercontent.com/77606152/152698059-1aba02e9-f50a-41f5-abe6-2df266286d20.png)<br>
B. Performance: Compared with Halide-HLS<br>
![image](https://user-images.githubusercontent.com/77606152/152698134-d03f96f9-010a-4269-8fee-e55147961fd0.png)<br>
C. FPGA vs CPU: <br>
![image](https://user-images.githubusercontent.com/77606152/152698174-8c60bdc2-24f7-4add-b58a-0a94b258710c.png)<br>

## 2022.2.5
### Paper: HeteroCL: A Multi-Paradigm Programming Infrastructure for Software-Defined Reconfigurable Computing<br>
### Author: Yi-Hsiang Lai, Yuze Chi, Jason Cong and Zhiru Zhang. <br> Publish: FPGA'2019<br>
#### Motivation:
FPGA has advantage in latency and power efficiency over CPU and GPU. And it is also helpful in doing prototype verification  for DSAs on FPGA. However,  in some extend it lacks programmability.<br>
#### Content:
1) A novel DSL is proposed which blends  declarative symbolic expressions with imperative code.<br>
2) It seperates algorithm and schedule to improve portability.<br>
3) Hardware custimizations including compute, data types and memory architectures are supported for performance optimization.<br>
4) Multiple backends are supoorted CPU, PolySA, SODA, Merlin compiler.<br>
5) Flow: HeteroCL -> Extended Halide IR -> DSL/IR (Poly,SODA, Merlin, LLVM) -> FPGA/CPU<br>
#### Experiment:
1) Platform: AWS EC2 f1.2xlarge instance 8 vCPU cores; Xilinx Virtex Ultra-Scale+??? VU9P FPGA @250MHz.<br>
2) Benchmark: KNN-based digit recognition, K-means, stencil, GEMM, CNN et.cl<br>
3) Baseline: CPU backend. MKL and TVM for systolic applications.
4) Result:<br>
A. Speedup over CPU by using Merlin backend<br>
![image](https://user-images.githubusercontent.com/77606152/152705279-c4552a81-8b05-4cf3-b622-f83f7ffdbe40.png)<br>
B. Speedup of stencil applicatoins by using SODA backend: quti:16bits fixed<br>
![image](https://user-images.githubusercontent.com/77606152/152705331-e826759a-6b40-4145-ad68-187c9b3dad35.png)<br>
C. Speedup of systolic array applicatoins by using PolySA backend: TVM[6], MKL[20]<br>
![image](https://user-images.githubusercontent.com/77606152/152705369-fb61b676-24a7-4c4e-bef6-d94ff6631352.png)<br>


## 2022.2.8
### Paper: DNNBuilder: an Automated Tool for Building High-Performance DNN Hardware Accelerators for FPGAs<br>
### Author: Xiaofan Zhang, Deming Chen and et.cl. <br> Publish: ICCAD???18<br>
#### Motivation:
Edge applications usually ask for real-time processing of streaming inputs that limit the FPGA???s ability to batch the data for increased throughput, as the additional latency incurred by batch process can exceed what is allowed by real-time performance requirements. Besides, it is challenging for edge device to process high-definition (HD) images/videos, which generates higher requirements for feature map storage and computation power. <br>
#### Content:
1) An end-to-end Framework(Caffe, Tensorflow) to FPGA automation tool is proposed.<br>
   A. Do training on exsisting Framework and generate model(.prototxt adn .caffemodel)
   B. Pass the model to Generate process. Parsing:Get model information. Optimization: Decide best schdule for low latency within hardware limitation. Construction: Generate pre-build RTL based on the parameter gotten from last step.<br>
   ![image](https://user-images.githubusercontent.com/77606152/153262470-520f2086-14ab-435f-a96c-465e4baa4ac0.png)<br>
2) Flexible quantization scheme: When the off-chip bandwidth exceeds the limitation(FC layer with low comm to comp ratio), it can retrain the model with lower bit precision.<br>
3) A fine-grained layer-based pipeline arhcitecture and a column based cache scheme for low latency and less hardware utilization.<br>
   A. Micro architecture: 4x3x3 (inc, h, w) infmap; 4x2x2x6 (inc,R,S,outc) kernel<br>
   ![image](https://user-images.githubusercontent.com/77606152/153263926-3ce96b3e-286f-4c8c-a3e2-40a071d1c744.png)<br>
   B. Column based line buffer <br>
   ![image](https://user-images.githubusercontent.com/77606152/153263984-4834dc72-c7bc-4a13-8809-6d5a9ba8a046.png)<br>
   C. Fine-grained pipelining scheme<br>
   ![image](https://user-images.githubusercontent.com/77606152/153961047-49625a75-f59e-4123-90d4-6b85a575cf11.png)<br>
   ![image](https://user-images.githubusercontent.com/77606152/153264180-ae67543c-f606-47be-9138-b5dbeaad2164.png)<br>
4) An automatic resource allocation management: First decide the DSP utilization, then balance the commnication and computation be allocating enough on-chip buffer.<br>
#### Experiment:
1) Platform: Xilinx XC7Z045 @200MHz, Xilinx KU115 220-235MHz.<br>
2) Benchmark: Alexnet, ZF, VGG16, YOLO<br>
3) Baseline: Other FPGA accelerator.
4) Result:<br>
A. Xilinx XC7Z0455 resources and performance<br>
![image](https://user-images.githubusercontent.com/77606152/153264934-f480bf64-f790-4017-bf28-3eacbe6f7a29.png)<br>
B. Xilinx KU115 resources and performance<br>
![image](https://user-images.githubusercontent.com/77606152/153264988-ab65783f-08db-4fec-a22a-c60631799f9c.png)<br>
C. Comparison with other FPGA based DNN accelerators<br>
![image](https://user-images.githubusercontent.com/77606152/153265073-01b7021a-bca3-47ec-83e9-57720c365492.png)<br>

## 2022.2.9
### Paper: DiGamma: Domain-aware Genetic Algorithm for HW-Mapping Co-optimization for DNN Accelerators<br>
### Author: Sheng-Chun Kao, Tushar Krishna and et.cl. <br>
#### Motivation:
HW-opt(given mapping strategy, search HW), and Mapping-opt(Fixed hardware, searching strategy) are widely studied. However, since the design space for co-exploration of mapping and hw are huge, how to do co-optimize for the HW and mapping is still an open question.<br>
#### Content:
1) A HW-Mapping co-optimization framework is proposed.<br>
   A. Takes target model, optimization objective, design budget, an optimization algorithm, and  a design constraint((optional, either fixed HW or Mapping). Generatean optimized design point.<br>
   ![image](https://user-images.githubusercontent.com/77606152/153335121-e8f77330-5796-4e96-99b4-3dd9c8ac57d8.png)<br>
   B. An excellent Optimization Algorithm(genetic algorithm) and Encode and Decode scheme.<br>
   ![image](https://user-images.githubusercontent.com/77606152/153335665-b4b17149-fbd9-43ba-af3c-d7bc8ce47997.png)<br>
#### Experiment:
1) Setup: Area(Only estimate PE and Buffer size, 0.2mm^2 for edge, 7.0mm^2 for cloud), Sampleing under 40K<br>
2) Benchmark: MobilenetV2, Resnet18, Resnet50, Mnasnet, BERT, DLRM, NCF.<br>
3) Baseline: Other optimization algorithms and HW-opt, Mapping-opt Schemes.<br>
4) Result:<br>
A. Algorithm comparsion<br>
![image](https://user-images.githubusercontent.com/77606152/153963346-3b1e5e4b-c3b0-4a04-aa9c-10fe6da7b85b.png)<br>
B. Co-optimized scheme versus HW-opt and Mapping-opt<br>
![image](https://user-images.githubusercontent.com/77606152/153963384-53f7590a-132a-40c9-aac2-522dbc2962d0.png)<br>

## 2022.2.11
### Paper: ESP4ML: Platform-Based Design of Systems-on-Chip for Embedded Machine Learning<br>
### Author: Davide Giri, Luca P. Carloni and et.cl. <br> 
#### Motivation:
To simplify the process of designing complete SoCs that can be rapidly prototyped on FPGA boards.<br>
#### Content:
1) ESP<br>
ESP designed a flexible tile-based architecture. Users can leverage SystemC and Cadence Stratus HLS to implement their Accelerator. And these Accelerator can be easily plugged in the SoC through the socket inferface to the multi-plane NOC.<br>
2) HLS4ML<br>
Provided a channel for connecting ML framework(Keras, Pytorch, ONNX) and Vivado HLS.<br>
3) New features proposed<br>
A. An updated integration flow is proposed: Vivado HLS and Stratus HLS are used to generate Accelerators, ESP integrates these Accs and build the SoC.<br>
![image](https://user-images.githubusercontent.com/77606152/153723672-bc95d8ee-8d32-4397-bb02-5896ae173267.png)<br>
B. A reconfigurable p2p communication channels are proposed. Support p2p data transfers among accelerators.<br>
C. A runtime system in Linux is proposed some that it can dynamically configure and manage and synchronize the Accs.<br>
D. Combined HLS4ML to the flow.<br>
#### Experiment:<br>
1) Platform: Xilinx Ultrascale FPGA @78MHz, Intel i7 8700K processor, NVIDIA Jetson TX1 embedded system<br>
2) Benchmark: Digit classification(MLP), Image denoising(Autoencoder model)<br>
3) Baseline: Kernels are invoked serially.<br>
4) Result:<br>
A. Energy efficiency:pipe, multi-tread:Kernels runs parallelly.<br>
![image](https://user-images.githubusercontent.com/77606152/153724107-8ab023c7-87d1-4210-9005-f9028c83f924.png)<br>
B. P2P energy saving<br>
![image](https://user-images.githubusercontent.com/77606152/153724160-85fb8f54-7afc-4a67-bc4a-73fc90bfac52.png)<br>

## 2022.2.12
### Paper: Heterogeneous Dataflow Accelerators for Multi-DNN Workloads<br>
### Author: Hyoukjun Kwon, Vikas Chandra and et.cl. <br> Publish: HPCA???21<br>
#### Motivation:
Emerging applications such as AR/VR lead to multi-DNN workloads, which includes various layer shape, layer operation. However, no single dataflow style is good for all the layers. An "heterogeneous dataflow Accelerators" architecture is proposed to achieve the global optimal EDP for multi-DNN Workloads bu combining different FDAs<br>
![image](https://user-images.githubusercontent.com/77606152/153783717-bd6a3a78-840d-480a-b51c-d2b3738ae0f7.png)
#### Conent:
1) A hardware and schedule co-design space exploration algorithm is proposed<br>
![image](https://user-images.githubusercontent.com/77606152/153783794-734b0161-96f4-47d5-97f9-eddb975db2c2.png)<br>
A. Optimized hardware resource distribution for various sub-accs.<br>
B. Optimized layer execution schedules on the sub-accs.<br>
Consideration: Load balance(The largest latency divided by the least one), dataflow preference.<br>
Constraints: Layer dependency(breadth first or depth first) and Memory size. <br>
Post processing: Layer change to eliminating redundant idle time.<br>
![image](https://user-images.githubusercontent.com/77606152/153786853-f8edd8df-0a56-467c-8d40-fb5c0f02b73e.png)<br>
2) A NVDLA and Shi-diannao based HDA design called Maelstrom is used to explore the scalability over three multi-DNN work-load  <br>
A. NVDLA: Weight-stationary dataflow, exploits weight reuse and has parallelism over input and output channels.Suitble for deep channel layers<br> 
A. Shi-diannao: Output-stationary dataflow, exploits output and convolutional reuse. Suitble for high resolution activation layers<br> 
#### Experiment:
1) Platform: CAD tools with 28nm technology library and MAESTRO<br>
2) Benchmark: <br>
![image](https://user-images.githubusercontent.com/77606152/153787469-00b65fd0-2e68-4d27-b629-15adf8ea5d98.png)<br>
3) Baseline: RDA and FDA.<br>
4) Result:<br>
A. Impact of Workloads compared with FDAs.<br>
VR-A: 63.26% Latency and 4.05% energy improvements.<br>
VR-B: 86.8% Latency and 6.61% energy improvements.<br>
MLPerf: 48.1% Latency and 4.4% energy improvements.<br>
B. Compared with RDA: <br>
VR-A: 22.9% more Latency and 18.7% energy improvements.<br>
VR-B: 21.5% more Latency and 15.5% energy improvements.<br>
MLPerf: 24.3% more Latency and 18.9% energy improvements.<br>
Maelstromand RDA cases are Pareto-optimal design points with different strengths in energy and latency.<br>

## 2022.2.12
### Paper: DNNWEAVER: From High-Level Deep Network Models to FPGA Acceleration<br>
### Author: Sharma, Hardik, et al.<br> Publish: the Workshop on Cognitive Architectures. 2016.<br>
#### Motivation:
Improve the programmability of FPGAs.
#### Conent:
1) A mocro-dataflow virtual machine for DNN ACCs: Actually its an interface for translating model to Verilog.<br>
2) Hand-optimized customizable Verilog template is proposed: DNN ACCs with settled dataflow and achitecture but can have different settings.<br>
3) An automatical compile flow from Caffe to Verilog.<br>
![image](https://user-images.githubusercontent.com/77606152/153951104-b2d5634d-6d65-4328-998e-397863381fd7.png)<br>
#### Experiment:
1) Platform: Xilinx ZC702 and Altera Stratix V SGSD5, ARM Cortex A15, Tegra K1, Tesla K40<br>
2) Benchmark: LeMet,Siamese, CIFAR-10 Full, CIFAR-10 Quick, Djinn ASR<br>
3) Baseline: Xeon E3CPU and GTX650Ti GPU.<br>
4) Result:<br>
A. Performance, compared with CPU:<br>
ZYNQ: 1.3x, Stratix:7.1x.<br>
![image](https://user-images.githubusercontent.com/77606152/153952244-6bd49227-5af2-4948-a424-628a81510e19.png)<br>
B. Performance, compared with GPU: <br>
ZYNQ: 0.05x, Stratix:0.3x compared with K40.<br>
![image](https://user-images.githubusercontent.com/77606152/153952588-c2da86fd-e571-4354-8e35-7a398731f484.png)<br>
C. Energy effciency, compared with CPU:<br>
ZYNQ: 36x, Stratix:20x.<br>
![image](https://user-images.githubusercontent.com/77606152/153953040-e00343ec-1357-4ab6-bcde-cb3ae9f3671b.png)<br>
D. Energy effciency, compared with GPU: <br>
ZYNQ: 3.6x, Stratix:2.0x compared with baseline.<br>
![image](https://user-images.githubusercontent.com/77606152/153953193-fcb6c6f7-7075-482f-81c4-7a12f8efdef0.png)<br>
