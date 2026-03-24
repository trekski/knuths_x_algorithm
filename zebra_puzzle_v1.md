# Applying Knuth's X Algorithm to solving Zebra Puzzles (a.k.a. Einstein's Puzzle)

This document explains my approach and work on the latter problem - the Zebra Puzzles.


## Abstract

...


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
Find a sub-collection $S^*$ - the smallest subset of the collection, such that $\bigcup S^* = U$.

A more sctrict formulation is the [Exact Cover](https://en.wikipedia.org/wiki/Exact_cover) problem, which additionally requires that all elememts of $S^*$ are pairwise disjoint.

One of the ways to represent some of the CSPs is encoding them as a n Exact Cover problem[^2][^3]. There are known methods to solve set cover problems, for example Knuth's X algorithm. Therefore if we are able to map a puzzle or a riddle to n exact cover problem, we can solve such a puzzle.

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

Applying Knuth's X algorithm to thesparse matrix solves the CSP it represtents. What we need though is to properly represent the solution elements and the cosntraints in the matrix.

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

## 2. How to model the Zebra Puzzle as a Set Coverage Problem

In parallel to Sudoku, to model the Zebra Puzzle as a sparse matrix for an Exact Cover problem, we need to decide:
- what are the elements of our Universe *U*
- what constraints does the puzzle pose, and how to map them to subsets of *U*

First we can observe that within the riddle text there is mentions of a number people living in the same number of houses. Each person has a set of attributes. For each person the attriburtes are along the same set of "dimensions" (e.g. each person has a "nationality", each person has a "favourite drink"), each with a range of possible values (e.g. for nationality it's: Norwegian, British, German, Swedish etc.). And no two persons share the same value along a common dimension - that is: there is one and only one person who's Norwegian, there is one and only one person who drionks milk, etc. Whaat follows from all the "one and only one" conditions, is that each person in the final solution needs to have a full set of attributes (Nationality, drink, pet, brand of sigarretes etc.) and a house number where they live.

Commonly[^7][^8][^9] this is then visualised as a grid (*not* yet the Knuth's sparse matrix) that has palces for each house and each *attribute dimension* where *attribute values* are placed, fulfilling the constraints.

<div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px;">
  <div>
    **available attributes**
    * Nationality : Norwegian
    * Nationality : British
    * Nationality : German
    * Nationality : Dutch
    * Nationality : Swedish
    * Drink : Milk
    * Drink : Water
    * ...
    * 
  </div>
  <div>
    **Partial solution**
    | |house #1|house #2|house #3|house #4|house #5|
    |-|---|---|---|---|---|
    | |   |   |   |   |   |
    | |   |   |   |   |   |
    | |   |   |   |   |   |
  </div>
</div>

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
[^7]: https://www.wikihow.com/Einstein%27s-Riddle
[^8]: https://medium.com/brainzilla/einsteins-riddle-step-by-step-tutorial-on-how-to-solve-the-world-s-hardest-puzzle-46bcf054a7c7
[^9]: https://youtu.be/HaNSHQOrSX8?t=1255