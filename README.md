# CSC343 Review 

## Table of Contents

* [Midterm Review](#midterm-review)
  * [Relational Algebra](#relational-algebra)
  * [Basic SQL](#basic-sql)
* [Embedded SQL](#embedded-sql)
* [Data Definition Language](#data-definition-language)
* [Design Theory for Relational Databases](#design-theory-for-relational-databases)
  * [Part I: Functional Dependency Theory](#functional-dependency-theory)
  * [Part II: Using FD Theory to do Database Design](#using-fd-theory-to-do-database-design)
    * [Minimal Basis](#minimal-basis)
    * [Find All Keys](#find-all-keys)
    * [Boyce-Codd Normal Form](#bcnf)
    * [3NF](#3nf)
    * [Chase Test](#chase-test)



# Midterm Review

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

## Relational Algebra

### Summary of operators

<img src="https://github.com/shin19991207/CSC343/blob/main/Images/Operation.jpeg" width="500">


#### Set operations

* Must have operands with same schema: same # of attributes with same name and same order
  * Valid: <img src="https://render.githubusercontent.com/render/math?math=R_1(a,b,c) \cup R_2(a,b,c)">
  * Invalid: <img src="https://render.githubusercontent.com/render/math?math=R_1(a,b,c) \cup R_2(b,a,c)">


    | Operation   | Symbol                                                     |
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
- Subtract the failures from all to get the answer.

## Basic SQL

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


### Bag vs Set Semantics 

#### Which is Used
* We saw that a SELECT-FROM-WHERE statement uses bag semantics by default. Duplicates are kept in the result.
* The set operations use set semantics by default. Duplicates are eliminated from the result.

#### Controlling Duplicate Elimination
* We can force the result of a SFW query to be a set by using SELECT DISTINCT ...
* We can force the result of a set operation to be a bag by using ALL, E.g. 
  ```sql
  (SELECT sid
   FROM Took
   WHERE grade > 95)
  UNION ALL
   (SELECT sid
   FROM Took
   WHERE grade < 50);
  ```

### View
A view is a relation defined in terms of stored tables (called base tables) and other views.
* Access a view like any base table.
* Two kinds of view:
  * Virtual: no tuples are stored; view is just a query for constructing the relation when needed.
  * Materialized: actually constructed and stored. Expensive to maintain!
* We’ll use only virtual views.

## Embedded SQL

### Call-level interface (CLI)
* C has SQL/CLI
* Java has JDBC 
* Python has psycopg2 (which we will use in this course)

### SQL injection
“Never, never, NEVER use Python string concatenation (+) or string parameters interpolation (%) to pass variables to a SQL query string. Not even at gunpoint.”

### Some Examples

* *static.py* 
  ```python
  """Demo of pscyopg2 with a static query, that is, one whose complete content
  is known at the time of writing the code.  Also demonstrates how to iterate
  over the results from a query.
  """

  # Import the psycopg2 library.
  import psycopg2

  # Create an object that holds a connection to your database.
  # Substitute your database name for mine, and your username for mine.
  # Password really is empty (and is unrelated to your Teaching Labs
  # password).
  conn = psycopg2.connect("dbname=csc343h-dianeh user=dianeh password=")

  # Open a cursor object that can hold the results of a query, and allow
  # us to iterate over them.
  cur = conn.cursor()

  # Execute some statements.
  cur.execute("set search_path to university;")

  # Execute a query and iterate over its results.
  cur.execute("SELECT * FROM Student;")
  for record in cur:
      # Record is a Python tuple. You can print it like any Python tuple.
      print(record)
      # And you can pull elements out by index.
      sid = record[0]
      cgpa = record[5]
      print(f'Student number was {sid} and cgpa was {cgpa}.')

  # Close communication with the database
  cur.close()
  conn.close()
  ```
  
* *dynamic.py*
  ```python
  """Demo of pscyopg2 with a dynamic SQL statement, that is, one whose complete 
  content is not known until we run the code.  This time, the statement is an
  INSERT rather than a query, so there are no results to iterate over.
  """

  # Import the psycopg2 library.
  import psycopg2

  # Create an object that holds a connection to your database.
  # Substitute your database name for mine, and your username for mine.
  # Password really is empty (and is unrelated to your Teaching Labs
  # password).
  conn = psycopg2.connect("dbname=csc343h-dianeh user=dianeh password=")

  # Open a cursor object that can hold the results of a query, and allow
  # us to iterate over them.
  cur = conn.cursor()

  # Get ready, and then ask the user what course they want to add.
  cur.execute("set search_path to university;")
  print('We are going to add a new course!')
  cnum = input('Course number: ')
  name = input('Course name: ')
  dept = input('Department: ')

  # Build up the statement using Python string operations, then execute it.
  cur.execute(f"INSERT INTO COURSE VALUES ({cnum}, '{name}', '{dept}');")

  # Close communication with the database.
  cur.close()
  conn.commit()
  conn.close()
  ```
  
* *safe.py*
  ```python
  """Demo of pscyopg2 with a dynamic SQL statement, that is, one whose complete
  content is not known until we run the code.  Again, the statement is an
  INSERT rather than a query, so there are no results to iterate over.  But this
  time, we execute the statement safely.
  """

  # Import the psycopg2 library.
  import psycopg2

  # Create an object that holds a connection to your database.
  # Substitute your database name for mine, and your username for mine.
  # Password really is empty (and is unrelated to your Teaching Labs
  # password).
  conn = psycopg2.connect("dbname=csc343h-dianeh user=dianeh password=")

  # Create a cursor object.
  cur = conn.cursor()

  # Get ready, and then ask the user what course they want to add.
  cur.execute("set search_path to university;")
  print('We are going to add a new course!')
  cnum = input('Course number: ')
  name = input('Course name: ')
  dept = input('Department: ')


  # -- The safe way --
  # Sending a separate parameter to the execute method is safe:
  # cur.execute('SELECT * FROM Took WHERE sid = %s;', (student_number,))
  cur.execute("INSERT INTO COURSE VALUES (%s, %s, %s);", (cnum, name, dept))


  # v2:
  # This looks similar but is VERY DIFFERENT! It is computing the complete
  # string in Python and sending that complete string to the execute method.
  # This is just as risky as how we did it before.
  # cur.execute('SELECT * FROM Took WHERE sid = %s;' % student_number) # NO!!
  # cur.execute(f"INSERT INTO COURSE VALUES (%s, '%s', '%s');" % (cnum, name, dept))


  # -- The original (unsafe) way --
  # The two unsafe approaches both compute the string *using Python string ops*
  # and send that complete string to the execute method. They just use an older 
  # and a newer syntax for string formatting.
  # cur.execute(f"INSERT INTO COURSE VALUES ({cnum}, '{name}', '{dept}');") # NO!!

  # Close communication with the database.
  cur.close()
  conn.commit()
  conn.close()
  ```
  
## Data Definition Language

### Built-in types

* CHAR(n): fixed-length string of n characters. Padded with blanks if necessary.
* VARCHAR(n): variable-length string of up to n characters.
* TEXT: variable-length, unlimited. Not in the SQL standard, but psql and others support it.
* INT =INTEGER 
* FLOAT =REAL
* BOOLEAN
* DATE; TIME; TIMESTAMP (date plus time)

### How NULLs Affect "UNIQUE"
* E.g., consider:
  ```sql
  create table Testunique (
     first varchar(25),
     last varchar(25),
     unique(first, last))
  ```
* This would prevent two insertions of ('Diane', 'Horton')
* But what about two insertions of (null, 'Schoeler’). For uniqueness, no two nulls are considered equal. So these inserts are allowed.

### How NULLs Affect “CHECK” Constraints
* A check constraint only fails if it evaluates to false. 
* It is not picky like a WHERE condition.
* E.g.: check (age > 0)

  <img src="https://github.com/shin19991207/CSC343/blob/main/Images/check_nulls.jpeg" width="500">

### Reaction Policies

#### What you can react to
* Your reaction policy can specify what to do either
  * **on delete**, i.e., when a deletion creates a dangling reference,
  * **on update**, i.e., when an update creates a dangling reference,
  * or both. Just put them one after the other.
* Example: `on delete restrict on update cascade`

#### What your reaction can be
* Your policy can specify one of these reactions (there are others):
  * **RESTRICT**: Don’t allow the deletion/update.
  * **CASCADE**: Make the same deletion/update in the referring row.
  * **SET NULL**: Set the corresponding value in the referring row to null.
  * If you say nothing, the default is to forbid the change in the referred-to table.

#### Updating the schema itself
* Alter: alter a domain or table
  ```sql
  alter table Course
     add column numSections integer;
  alter table Course
     drop column breadth;
  ```
* Drop: remove a domain, table, or whole schema. `drop table course;`
* Delete: delete all rows from a table. `delete from course;`
* If you drop a table that is referenced by another table, you must specify “cascade”. This removes all referring rows.

## Design Theory for Relational Databases

Get a schema that is in a “normal form” that guarantees good properties.
* “Normal” in the sense of conforming to a standard.
* The process of converting a schema to a normal form is called normalization.

### Functional Dependency Theory

* Principle: Avoid redundancy
* Redundant data can lead to anomalies.
  * Update anomaly: if we update only one tuple, the data is inconsistent!
  * Deletion anomaly: if we lose track of some data!
* Definition of FD
  * Suppose R is a relation, and X and Y are subsets of the attributes of R.
  * <img src="https://render.githubusercontent.com/render/math?math=X \to Y"> asserts that: If two tuples agree on all the attributes in set X, they must also agree on all the attributes in set Y.

### Using FD Theory to do Database Design

### Minimal Basis

Find a minimal basis for a set of FDs `S`:

* Step 1: Split the RHSs to get an initial set of FDs `S1`
* Step 2: For each FD, try to reduce LHS by projecting on its LHS `S2`
* Step 3: Try to eliminate each FD `fd` in `S2` by projecting on its LHS using the set of FDs `S2-{fd, removed_fds}`

### Find All Keys

| on LHS? | on RHS? | conclusions |
| ------- | ------- | ----------- |
| ✔️ | ✖️ | in every key |
| ✔️ | ✔️ | must check |
| ✖️ | ✔️ | not in any key |
| ✖️ | ✖️ | in every key |

* Find all keys on the projection on "must check" + "in every key" in all combinations.

### BCNF

* A relation R is in BCNF (Boyce-Codd Normal Form) if for every non-trivial FD <img src="https://render.githubusercontent.com/render/math?math=X \to Y"> that holds in R, X is a superkey

  * `nontrival` means Y is not contained in X
  * A superkey (<img src="https://render.githubusercontent.com/render/math?math=LHS^+ = ">all attributes in the relation) doesn't havbe to be minimal

* BCNF Decomposition 

  * R is a relation; F is a set of FDs. Return the BCNF decomposition of R, given these FDs. 

    ```
    BCNF_decomp(R, F):
      If an FD X -> Y in F violates BCNF
        Compute X^+.
        Replace R by two relations with schemas:
          R_1 = X^+
          R_2 = R - (X^+ - X)
        Project the FD's F onto R_1 and R_2.
        Recursively decompose R_1 and R_2 into BCNF.
    ```

  * If more than one FD violates BCNF, you may decompose based on any one of them. 

  * The new relations we create may not be in BCNF. We must recurse.

### 3NF 

* 3rd Normal Form (3NF) modifies the BCNF condition to be less strict.

* An attribute is prime if it is a member of any key.

* <img src="https://render.githubusercontent.com/render/math?math=X\to A"> violates 3NF iff X is not a superkey and A is not prime.

* 3NF synthesis

  * F is a set of FDs; L is a set of attributes. Synthesize and return a schema in 3NF.

    ```
    3NF_synthesis(F, L):
      Construct a minimal basis M for F.
      For each FD X -> Y in M
        Define a new relation with schema X \union Y.
      If no relation is a superkey for L
        Add a relation whose schema is some key.
    ```

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

* What 3NF decompositin offers:

  ✖️ No anomalies
    * 3NF allows FDs with a non-superkey on the LHS. This allows redundancy, and thus anomalies.

  ✔️ Lossless Join

  ✔️ Dependency Preservation

### Chase Test

#### The algorithm
 1. If two rows agree in the left side of a FD, make their right sides agree too.
 2. Always replace a subscripted symbol by the corresponding unsubscripted one, if possible.
 3. If we ever get a completely unsubscripted row, we know any tuple in the project-join is in the original (i.e., the join is lossless).
 4. Otherwise, the final tableau is a counterexample (i.e., the join is lossy).

#### Example

Show: If a tuple <a, b, c, d> is in <img src="https://render.githubusercontent.com/render/math?math=S_1 \bowtie S_2 \bowtie S_3">, it is in R.

Assume <a, b, c, d> is in <img src="https://render.githubusercontent.com/render/math?math=S_1 \bowtie S_2 \bowtie S_3">.

The Chase Test succeeded and thus lossless join decomposition if <a, b, c, d> was in R.

