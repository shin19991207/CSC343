# CSC343 Review 

## Table of Contents

[TOC]

## Week 8

### Minimal Basis

Find a minimal basis for a set of FDs `S`:

* Step 1: Split the RHSs to get an initial set of FDs `S1`
* Step 2: For each FD, try to reduce LHS by projecting on its LHS `S2`
* Step 3: Try to eliminate each FD `fd` in `S2` by projecting on its LHS using the set of FDs `S2-{fd, removed_fds}`



## Week 9

### BCNF (Boyce-Codd Normal Form)

* A relation R is in BCNF if for every non-trivial FD <img src="https://render.githubusercontent.com/render/math?math=X \to Y"> that holds in R, X is a superkey

  * `nontrival` means Y is not contained in X
  * A superkey (<img src="https://render.githubusercontent.com/render/math?math=LHS^+ = ">all attributes in the relation) doesn't havbe to be minimal

* BCNF Decomposition 

  * R is a relation; F is a set of FDs. Return the BCNF decomposition of R, given these FDs. 

    ![IMG_5D35B7AA72B4-1](/Users/morganchang/Desktop/IMG_5D35B7AA72B4-1.jpeg)

  * If more than one FD violates BCNF, you may decompose based on any one of them. 

  * The new relations we create may not be in BCNF. We must recurse.

### 3NF 

* 3rd Normal Form (3NF) modifies the BCNF condition to be less strict.

* An attribute is prime if it is a member of any key.

* <img src="https://render.githubusercontent.com/render/math?math=X\to A"> violates 3NF iff X is not a superkey and A is not prime.

* 3NF synthesis

  * F is a set of FDs; L is a set of attributes. Synthesize and return a schema in 3NF.

    ​			![IMG_1820](/Users/morganchang/Downloads/IMG_1820.jpg)

### Properties of Decompositions

* What we want from a decomposition 

  * No anomolies.

  * Lossless Join: It should be possible to 

    * project the original relations onto the decomposed schema
    * then reconstruct the original by joining. 

    We should get back exactly the original tuples.

  * Dependency Preservation: All the original FD's should be satisfied.

* What BCNF decompositin offers:

  ✔️ No anomalies

  ✔️ Lossless Join

  ✖️ Dependency Preservation
