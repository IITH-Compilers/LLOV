# LLOV:  A Fast Static Data-Race Checker for OpenMP Programs.  

LLOV stands for LLVM OpenMP Verifier.  It is a static data race detection 
tools in LLVM for OpenMP Programs.
LLOV can detect data races in OpenMP v4.5 programs written in C/C++ and FORTRAN.  

LLOV uses polyhedral compilation techniques to detect race conditions at 
compile time.  

Unlike other race detection tool, LLOV can mark a region of code as Data 
Race Free.

## Requirements
We use git LFS as the size of binaries corssed 100 MB.  
Install git-lfs package and enable git lfs hooks.  
For Ubuntu and Debian-  
```
sudo apt-get install git-lfs
git lfs install
```
These steps are required before fetching the repository.  


To run the regression rests, python package lit is required.  
```
python -m pip install --upgrade --force-reinstall pip
pip install lit --user
```


## How to Run LLOV
If OpenMP is not installed on your system or the path is not properly set,  
you can point to the included header & lib with additional compiler flags.  
`-Iinclude/ -Llib/`

#### Running LLOV on OpenMP C/C++ code from clang
```
./bin/clang -Xclang -disable-O0-optnone -Xclang -load -Xclang ./lib/OpenMPVerify.so  \
  -fopenmp -g test/1.race1.c
./bin/clang++ -Xclang -disable-O0-optnone -Xclang -load -Xclang ./lib/OpenMPVerify.so  \
  -fopenmp -g test.cpp
```

#### Running LLOV on OpenMP C/C++ code from opt
```
./bin/clang -fopenmp -S -emit-llvm -g test/1.race1.c -o test.ll
./bin/opt -mem2reg test.ll -S -o test.ssa.ll
./bin/opt -load ./lib/OpenMPVerify.so -openmp-forceinline \
  -inline -openmp-resetbounds test.ssa.ll -S -o test.resetbounds.ll
./bin/opt -load ./lib/OpenMPVerify.so \
  -disable-output \
  -openmp-verify \
  test.resetbounds.ll
```

#### Running LLOV on OpenMP FORTRAN code
```
flang -fopenmp -S -emit-llvm -g test.f95 -o test.ll
./bin/opt -O1 test.ll -S -o test.ssa.ll
./bin/opt -load ./lib/OpenMPVerify.so -openmp-forceinline \
  -inline -openmp-resetbounds test.ssa.ll -S -o test.resetbounds.ll
./bin/opt -load ./lib/OpenMPVerify.so \
  -disable-output \
  -openmp-verify \
  test.resetbounds.ll
```
For more FORTRAN examples with known race conditions, check out our 
microbenchmark 
[DataRaceBench FORTRAN](https://github.com/utpalbora/drb_fortran)

## Authors
Utpal Bora <cs14mtech11017@iith.ac.in>.  

## Credits
The following people contirbuted to LLOV in different ways.  
Pankaj Kukreja &lt;cs15btech11029@iith.ac.in&gt;  
Santanu Das &lt;cs15mtech11018@iith.ac.in&gt;  
Saurabh Joshi [website](https://sbjoshi.github.io)  
Ramakrishna Upadrasta [website](https://www.iith.ac.in/~ramakrishna/)  
Sanjay Rajopadhye [website](https://www.cs.colostate.edu/~svr/)  

## Release
Source of LLOV will be released soon under BSD license.  

## Docker container
Docker Registry: [hub.docker.com](https://hub.docker.com/r/utpalbora/llvm/tags)  
repository: llvm  
tag: llov  

The docker image contians LLOV, along with the following race detection tools-  
TSan-LLVM, Archer, SWORD, Helgrind, and Valgrind DRD.  

There are three OpenMP benchmarks for experimentation-  
[DataRaceBench v1.2](https://github.com/LLNL/dataracebench),  
[DataRaceBench FORTRAN](https://github.com/utpalbora/drb_fortran), and  
[OmpSCR v2.0](https://github.com/utpalbora/OmpSCR_v2.0).  

## How to cite LLOV in a publication

```
@article{Bora/taco/2020,
  author = {Bora, Utpal and Das, Santanu and Kukreja, Pankaj and Joshi, Saurabh and Upadrasta, Ramakrishna and Rajopadhye, Sanjay},
  title = {LLOV: A Fast Static Data-Race Checker for OpenMP Programs},
  year = {2020},
  issue_date = {November 2020},
  publisher = {Association for Computing Machinery},
  address = {New York, NY, USA},
  volume = {17},
  number = {4},
  issn = {1544-3566},
  url = {https://doi.org/10.1145/3418597},
  doi = {10.1145/3418597},
  abstract = {In the era of Exascale computing, writing efficient parallel programs is indispensable, and, at the same time, writing sound parallel programs is very difficult. Specifying parallelism with frameworks such as OpenMP is relatively easy, but data races in these programs are an important source of bugs. In this article, we propose LLOV, a fast, lightweight, language agnostic, and static data race checker for OpenMP programs based on the LLVM compiler framework. We compare LLOV with other state-of-the-art data race checkers on a variety of well-established benchmarks. We show that the precision, accuracy, and the F1 score of LLOV is comparable to other checkers while being orders of magnitude faster. To the best of our knowledge, LLOV is the only tool among the state-of-the-art data race checkers that can verify a C/C++ or FORTRAN program to be data race free.},
  journal = {ACM Trans. Archit. Code Optim.},
  month = dec,
  articleno = {35},
  numpages = {26},
  keywords = {OpenMP, program verification, polyhedral compilation, static analysis, data race detection, shared memory programming}
}
```

## Current Limitations
Following are the limitations of the current version of LLOV.  
* Does not support explicit synchronization in OpenMP. This might result 
  in FN cases for programs with dependences across SCoPs.
* Does not handle dynamic control flow.
* Does not support target offloading constructs.
* Does not support OpenMP tasks.
* Can not handle irregular accesses (a[b[i]]).
* Might produce FP for tiled and/or parallel code generated by 
  automatic code generation tools such as Pluto, Polly and PolyOPT.
* Might produce FP for OpenMP sections construct.
*  Function calls within OpenMP constructs are handled only if the 
  function can be inlined.
* For some cases, source line number mapping in Polly is not preserved. 
  For those cases, race is flagged at the corresponding loop line number.

## Contact
If you have any query, please contact "Utpal Bora" &lt;cs14mtech11017@iith.ac.in&gt;.  
Please file a bug if you find the race checker is not working as required.  

Regards,  
Utpal

