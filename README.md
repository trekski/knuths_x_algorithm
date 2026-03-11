# Applying Knuth's X Algorithm to solving Zebra Puzzles (a.k.a. Einstein's Puzzle)

In this repositry I keep progrmas that I wrote to implement Knuth's X algorithm in order to better understand how the algorithm works. I started by creating a simple Sudoku solver. Then, when I got a better grip on the X algorithm, I attempted applying it to solve the so called [Zebra Puzzles](https://en.wikipedia.org/wiki/Zebra_Puzzle) (a.k.a Einstein's Riddle)

This documetn explaisn my approach and work on the latter problem - the Zebra Puzzles.

## 0. Repository contents

Before I get into explaining my approach and results, here's a quick rundown of the contents of the repository, in suggested order of reading:

* `README.md` this file, explaining what was done and how 
* `knuth_algo_x_sudoku.ipynb` - a warm-up exercise - implementing Knuth's algorithm X to solve a sudoku
  * `input_n.sudoku` - several examples of sudoku that can be solved by the above program. Coding of a sudoku in this file should be rather self-explanatory
* `knuth_algo_x_einstein.ipynb` - my first attempt to implement a solver for Einstein Puzzles using Knuth's X algorithm
  * `input_n_einstein.yml` - several examples of Einstein puzzles that can be solved using the above program
* `scratshpad` - a  "scratchpad" directory with some w.i.p. versions of some of the code. Can safely be ignored.

## 1. Prerequisites

A prerequsite to follow my explanation of how I used the X algorithm to solve Zebra Puzzles, is to understand the following concepts:

### 1.1. Constrain satisfaction and set coverage problems

A [Constraint Satisfaction Problem](https://en.wikipedia.org/wiki/Constraint_satisfaction_problem)(CSP) is a "mathematical question defined as a set of objects whose state must satisfy a number of constraints or limitations."[^1]

Many logical puzzles can be modeled as a CSP[^1], including but not limited to Sudoku and Einstein's Riddle.

A [Set Cover problem](https://en.wikipedia.org/wiki/Set_cover_problem) 

One of the ways to represent some of the CSPs is encoding them as a Set Cover Problem. More specifically: an exact set cover problem is an example of a CSP[^2][^3]. There are known methods to solve set cover problems. Therefore if we are able to map a puzzle or a riddle to a set cover problem, we can solve such a puzzle.



#### Sudoku as an example of a set coverage problem

### 1.3. Knuth's X Algorithm using a sparse matrix

### 1.4 What is a "Zebra Puzzle"?

## 2. How to model the Zebra Puzzle as a set Coverage problem

### 2.1. Obvious naive representation 

### 2.2 First challenge : "identity" contraints

#### Workaround : attribute sets as solution elements

### 2.3 Second challenge : directied neighbor cosntraints

#### Solution : additional matrix columns

### 2.4. Third challenge : non-directed neighbor cosntraints

#### Solution : even mode additional matrix columns and rows

## 3. Implementation considerations

### 3.1. Number of combinations and "row culling"

### 3.2. Filling in the sparse matrix

## 4. Observations

### 4.1. solution times

### 4.2. More advanced puzzles

### 4.3. Going back to the "naive" approach


[^1]: https://en.wikipedia.org/wiki/Constraint_satisfaction_problem
[^3]: https://en.wikipedia.org/wiki/Set_cover_problem#Related_problems
[^2]: https://en.wikipedia.org/wiki/Exact_cover