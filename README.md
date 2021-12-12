# CSC343 Review 

## Table of Contents

[TOC]

[Midterm Review](#midterm-review)



## Midterm Review

### Terminology 

* Schema: Teams(Name, HomeField, Coach)

* Instance: data in a relation

* Relation: table

* Attribute: column

* Tuple: row

* Arity of a relation: number of attributes

* Cardinality of a relation: number of tuples

* Math relation (relational algebra) is a set of tuples: There can be no duplicate tuples. Order doesn’t matter.

* Key: a minimal set of attributes such that no two tuples can have the same values on all of these attributes

* Superkey: any superset of a (some) key

* Foreign keys: the referring attribute is called a foreign key because it refers to an attribute that is a key in another table.

* Referential integrity constraints: These R1[X] ⊆ R2[Y] relationships are a kind of referential integrity constraint.

* Not all referential integrity constraints are foreign key constraints. R1[X] ⊆ R2[Y] is a foreign key constraint iff Y is a key for relation R2.



### Summary of operators

<img src="https://github.com/shin19991207/CSC343/blob/main/Images/Operation.jpeg" width="500">


#### Set operations

* Must have operands with same schema: same # of attributes with same name and same order
  * Valid: <img src="https://render.githubusercontent.com/render/math?math=R_1(a,b,c) \cup R_2(a,b,c)">
  * Invalid: <img src="https://render.githubusercontent.com/render/math?math=R_1(a,b,c) \cup R_2(b,a,c)">

| Operator   | Notation                                                     |
| ---------- | ------------------------------------------------------------ |
| Intersect  | <img src="https://render.githubusercontent.com/render/math?math=\cap = \bowtie"> |
| Union      | <img src="https://render.githubusercontent.com/render/math?math=\cup"> |
| Difference | <img src="https://render.githubusercontent.com/render/math?math=-"> |



### Specific types of query

#### Max (min is analogous):

- Pair tuples and find those that are not the max.
- Then subtract from all to find the maxes.

#### k or more:

- Make all combos of k different tuples that satisfy the condition.

#### Exactly k:

- (k or more) - ((k + 1)or more)

#### At most k:

- All - ((k + 1)or more)

#### Every:

- Make all combos that should have occurred .
- Subtract those that did occur to find those that didn’t always (These are failures.)
- Subtract the failures fro ball to get the answer.



### SQL query structure

```sql
CREATE VIEW name AS

SELECT

FROM

WHERE

GROUP BY

HAVING

ORDER BY DESC;
```

#### Order:

1. From 1 or more tables
2. Where to filter rows
3. Group by to organize
4. Having to filter groups
5. Select to choose what to represent
6. Order by



### Subquery

* Name the result

  ```sql
  SELECT sid, dept||cnum as course, grade
  
  FROM Took, (SELECT * 
  
  						FROM Offering 
  
  				 	  WHERE instructor = ‘Horton’) Hoffering
  
  WHERE Took.oid = Hoffering.oid;
  ```

- If a subquery is guanranteed to produce exactly one tuple, the it can be used as a value.



### Quantifying over multiple results

- cgpa > SOME (subquery)
- cgpa > ALL (subquery)

### Join

- Cartesian product:
  - A CROSS JOIN B

- Theta-join:
  - A JOIN B ON C
  - A {LEFT|RIGHT|FULL} JOIN B ON C

- Natural join
  - A NATURAL JOIN B
  - A NATURAL {LEFT|RIGHT|FULL} JOIN B

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
