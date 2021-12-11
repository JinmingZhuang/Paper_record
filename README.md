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
