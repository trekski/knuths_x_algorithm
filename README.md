# Applying Knuth's X Algorithm to solving Zebra Puzzles (a.k.a. Einstein's Puzzle)

In this repositry I keep progrmas that I wrote to implement Knuth's X algorithm in order to better understand how the algorithm works. I started by creating a simple Sudoku solver. Then, when I got a better grip on the X algorithm, I attempted applying it to solve the so called [Zebra Puzzles](https://en.wikipedia.org/wiki/Zebra_Puzzle) (a.k.a Einstein's Riddle)

This document explains my approach and work on the latter problem - the Zebra Puzzles.

*Repository contents*

Before I get into explaining my approach and results, here's a quick rundown of the contents of the repository, in suggested order of reading:

* `README.md` this file, explaining what was done and how 
* `knuth_algo_x_sudoku.ipynb` - a warm-up exercise - implementing Knuth's algorithm X to solve a sudoku
  * `input_n.sudoku` - several examples of sudoku that can be solved by the above program. Coding of a sudoku in this file should be rather self-explanatory
* `knuth_algo_x_einstein.ipynb` - my first attempt to implement a solver for Einstein Puzzles using Knuth's X algorithm
  * `input_n_einstein.yml` - several examples of Einstein puzzles that can be solved using the above program
* `scratshpad` - a  "scratchpad" directory with some w.i.p. versions of some of the code. Can safely be ignored.
 
## Abstract


## 1. Prerequisites

A prerequsite to follow my explanation of how I used the X algorithm to solve Zebra Puzzles, is to understand the concepts refered to in this section. This section serves only as a reminder, not a full course or explanation of each of the concepts.

### 1.1. Constrain satisfaction and set coverage problems

A [Constraint Satisfaction Problem](https://en.wikipedia.org/wiki/Constraint_satisfaction_problem)(CSP) is a "mathematical question defined as a set of objects whose state must satisfy a number of constraints or limitations."[^1]

Many logical puzzles can be modeled as a CSP[^1], including but not limited to Sudoku and Einstein's Riddle.

A [Set Cover problem](https://en.wikipedia.org/wiki/Set_cover_problem) can be worded as :
Given a set *U*  (the universe), and a collection *S* of a given *m* subsets of *U* whose union equals the universe, the set cover problem is to identify a smallest sub-collection of *S* whose union equals the universe.

In other words, given a set *U* (the universe) and a set *S* (the collection) such that:
$$
S = \{x : x \in U \}
\\
\bigcup S = U
$$
Find a sub-collection $S^*$ - the smallest subset of the collection, such that $\bigcup X = U$
A more sctrict formulation is the [Exact Cover]() problem, which additionally requires that all elememts of $S^*$ are pairwise disjoint.

One of the ways to represent some of the CSPs is encoding them as a Set Cover Problem. More specifically: an exact set cover problem is an example of a CSP[^2][^3]. There are known methods to solve set cover problems. Therefore if we are able to map a puzzle or a riddle to a set cover problem, we can solve such a puzzle.

### 1.2. Sudoku as an example of a set coverage problem

Sudoku can be modelled as an exact cover problem[^4] as follows:
- **elements** the Universe $U$ are representing specific **constraints** of the sudoku puzzle, such as:
  - "there is a `1` in the first row"
  - "there is a `2` in the first row"
  - "there is a `3` in the first row"
  - ...
  - "there is a `9` in the first row"
  - "there is a `1` in the second row"
  - ...
  - "there is a `8` in the ninth row"
  - "there is a `9` in the ninth row"
  - "there is a `1` in the first column"
  - ...
  - "there is a `9` in the ninth column"
  - "there is a `1` in the first 3x3 box"
  - ...
  - "there is a `9` in the ninth 3x3 box"
  - "the grid position (1,1) has a number in it
  - "the grid position (1,2) has a number in it
  - ...
  - "the grid position (9,9) has a number in it
- each **possibility** of putting a number in any of the grid positions represetns a fulfillment of **several of the constraints** - i.e. it is a subset of $U$. For example:
  - putting the digit '5' in grid position (2,3) fulfills the following constraints:
    - "there is a `5` in the second row"
    - "there is a `5` in the third column"
    - "there is a `5` in the fist 3x3 box"
    - "the grid position (2, 3) has a number in it
  - putting the digit '7' in grid position (4,5) fulfills the following constraints:
    - "there is a `7` in the fourth row"
    - "there is a `7` in the fifth column"
    - "there is a `8` in the fourth 3x3 box"
    - "the grid position (4,5) has a number in it
    - etc.
- each cosntraint must be fulfilled **exactly once** (if there is a `5` in row *x*, there is exactly **one** `5` in row *x*, etc.)
  
### 1.3. Knuth's X Algorithm using a sparse matrix

"Algorithm X is an algorithm for solving the exact cover problem. It is a straightforward recursive, nondeterministic, depth-first, backtracking algorithm used by Donald Knuth to demonstrate an efficient implementation called DLX, which uses the dancing links technique."[^5]

To do it it works on a sparse matrix that is an incidence matrix of which solution elements satisfy which cosntraints. THat is in the matrix:
- columns represent the constraints / elements of the *U* universe
- rows represent available options / elements of the *S* collection of subsets of *U*
- matrix is filled with `0`` by default except for...
- if a given option/member of *S* (row) fulfills/contains a given cosntraint/element of *U* (column), a `1` is put instead

Applying Knuth's X algorithm solves the CSP it represtents.
What we need though is to properly represent the solution elements and the cosntraints in the matrix.

### 1.4 What is a "Zebra Puzzle"?

The "Zebra Puzzle", alo known as "the Einstein Riddle" is a logic puzzle known in many vartations, most notably in the following (attributed to Einstein, hence the alternative name)[^6]

> There are five houses.
> - The Englishman lives in the red house.
> - The Spaniard owns the dog.
> - Coffee is drunk in the green house.
> - The Ukrainian drinks tea.
> - The green house is immediately to the right of the ivory house.
> - The Old Gold smoker owns snails.
> - Kools are smoked in the yellow house.
> - Milk is drunk in the middle house.
> - The Norwegian lives in the first house.
> - The man who smokes Chesterfields lives in the house next to the man with the fox.
> - Kools are smoked in the house next to the house where the horse is kept.
> - The Lucky Strike smoker drinks orange juice.
> - The Japanese smokes Parliaments.
> - The Norwegian lives next to the blue house.
>
> Now, who drinks water? Who owns the zebra? 
> In the interest of clarity, it must be added that each of the five houses is painted a different color, and their inhabitants are of different national extractions, own different pets, drink different beverages and smoke different brands of American cigarets. One other thing: in statement 6, right means your right. 

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

### 3.2. Iterative filling in the sparse matrix

## 4. Observations

### 4.1. Solution times

### 4.2. More advanced puzzles

## 5. Conclusion and next steps

#### Going back to the "naive" approach


[^1]: https://en.wikipedia.org/wiki/Constraint_satisfaction_problem
[^2]: https://en.wikipedia.org/wiki/Set_cover_problem#Related_problems
[^3]: https://en.wikipedia.org/wiki/Exact_cover
[^4]:  https://en.wikipedia.org/wiki/Sudoku_solving_algorithms#Constraint_programming
[^5]: https://en.wikipedia.org/wiki/Knuth%27s_Algorithm_X
[^6]: https://en.wikipedia.org/wiki/Zebra_Puzzle