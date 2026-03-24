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
Given a set *U*  (the universe), and a collection ***S*** of a given ***m*** subsets of ***U*** whose union equals the universe, the set cover problem is to identify a smallest sub-collection of ***S*** whose union equals the universe.

In other words, given a set ***U*** (the universe) and a set ***S*** (the collection) such that:

$$S = \{x : x \in U \}\\
\bigcup S = U$$

Find a sub-collection $`S^*`$ - the smallest subset of the collection, such that $`\bigcup S^* = U`$.

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
- columns represent the constraints / elements of the ***U*** universe
- rows represent available options / elements of the ***S*** collection of subsets of ***U***
- matrix is filled with `0`` by default except for...
- if a given option/member of ***S*** (row) fulfills/contains a given cosntraint/element of ***U*** (column), a `1` is put instead

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
- what are the elements of our Universe ***U***
- what constraints does the puzzle pose, and how to map them to subsets of ***U***

First we can observe that within the riddle text there is mentions of a number people living in the same number of houses. Each person has a set of attributes. For each person the attriburtes are along the same set of "dimensions" (e.g. each person has a "nationality", each person has a "favourite drink"), each with a range of possible values (e.g. for nationality it's: Norwegian, British, German, Swedish etc.). And no two persons share the same value along a common dimension - that is: there is one and only one person who's Norwegian, there is one and only one person who drionks milk, etc. Whaat follows from all the "one and only one" constraints, is that each person in the final solution needs to have a full set of attributes (Nationality, drink, pet, brand of sigarretes etc.) and a house number where they live.

Commonly[^7][^8][^9] this is then visualised as a grid (*not* yet the Knuth's sparse matrix) that has palces for each house and each *attribute dimension* where *attribute values* are placed, fulfilling the constraints.

**available attributes**
* Nationality : Norwegian
* ~~Nationality : British~~
* Nationality : German
* Nationality : Dutch
* Nationality : Swedish
* Drink : Milk
* ~~Drink : Water~~
* ...

**Partial solution**
| |house #1|house #2|house #3|house #4|house #5|
|-|---|---|---|---|---|
|Nationality|?|?|British|?|?|
|Drink|?|Water|?|?|?|
|Cigarette|?|?|?|?|?|
|Pet|?|?|?|?|?|
|Color|?|?|?|?|?|

Apart from the "trivial" constraints of the "one and only one" type, we can identify further constraint types in the riddle, as follows:
* **"identity" constraints** - any statement that forces a person living in a house to have two attributes consiciding, not necessarily naming the exact hosue number. These are statements in the form "*The person whose `dimension_a` has value `value_a` also has `value_b` in `dimension_b`*", For example:
  > "*The Spaniard owns the dog*"

  `dimension_a` = "nationality",\
  `value_a` = "Spanish",\
  `dimension_b` = "pet",\
  `value_b` = "dog"
* **"directed neighbor constraints"** - any statement that forces two people living next to each other to have their attributes consiciding, not  naming the exact hosue number. These are statements in the form "*The person whose `dimension_a` has value `value_a` lives on the `direction` of the person whose `dimension_b` has value `value_b`*", For example
  > "*The green house is immediately to the right of the ivory house.*"

  `dimension_a` = "color",\
  `value_a` = "green",\
  `dimension_b` = "color",\
  `value_b` = "ivory",\
  `direction` = "right"
* **"non-directional neighbor constraints"** - similar to directed neighbor constraints, with the difference that instead of a specific direction ("left" or "right") the constraint is thatthe two people simply live "next to" each other". For example:
  > "*The Norwegian lives next to the blue house.*"

### 2.1. Obvious naive representation

Such a visualisation of the problem suggests that:
- Elements of *U* could be all possible "one and only one" constraints:
  - each attribute is used only once:
    - a person with `nationality` = `Norwegian` lives somewhere
    - a person with `nationality` = `English` lives somewhere
    - a person with `nationality` = `Norwegian` lives somewhere
    - ...
    - a person with `pet` = `cat` lives somewhere
    - ...
  - each house has a person with exactly one of each attributes:
    - the person living in house `1` has exactly one `nationality` value
    - the person living in house `1` has exactly one `drink` value
    - the person living in house `1` has exactly one `cigarette` value
    - the person living in house `1` has exactly one `pet` value
    - the person living in house `1` has exactly one `house color` value
    - the person living in house `2` has exactly one `nationality` value
    - ...

elements of ***S*** could simply be paris of attribute and house number. In other a single element would describe a statement like "*The person who's `dimension` has `value` lives in house number `#`*".

* "person with `nationality` = `Norwegian` lives in house `1`"
* "person with `nationality` = `Norwegian` lives in house `2`"
* "person with `nationality` = `Norwegian` lives in house `3`"
* "person with `nationality` = `Norwegian` lives in house `4`"
* "person with `nationality` = `Norwegian` lives in house `5`"
* "person with `nationality` = `British` lives in house `1`"
* ...

However, the puzzle has other constrints too, which are not yet represented in the above example (yet)

### 2.2 First challenge : "identity" contraints

On the face of it, the rerpesentation proposed above immediately fails to be able to rerpesent the "identity" constraints.
After all, the rerpesetnation is designed mostly towards ensuring each attribute is used only once and, but identity type constraint requires two different attributes to be used. In other word this would be a constraint that is fulfuilled not by "one and exactly one" element of *S* but by two elements.

Let's take for example the statement "*Coffee is drunk in the green house*". Given the proposed ***S***, this "constraint" requires us to choose a pair of elements
* either "*`drink` = `coffee` lives in house `1`*" and "*`house color` = `green` lives in house `1`*"
* or "*`drink` = `coffee` lives in house `2`*" and "*`house color` = `green` lives in house `2`*"
* or "*`drink` = `coffee` lives in house `3`*" and "*`house color` = `green` lives in house `3`*"
* or "*`drink` = `coffee` lives in house `4`*" and "*`house color` = `green` lives in house `4`*"
* or "*`drink` = `coffee` lives in house `5`*" and "*`house color` = `green` lives in house `5`*"

Somehow one constraint requires two elements. This means that either we cannot model the Zebra Puzzle as an exact cover problem, or we have to re-engineer how we construct ***S*** and map puzzle text into constraints.

#### Workaround : attribute sets as solution elements

A workaround to the above problem I came up with is to construct the elements of ***S*** not as single attributes placed in slots of the solution grid, but as full columns. That is:

**Naive approach**
element of ***S*** placiong an attribute in the grid. For example:

1. statement: "*person with `pet` = `cow` lives in house `4`*"

2. grid fill-in:
   | |house #1|house #2|house #3|house #4|house #5|
   |-|---|---|---|---|---|
   |Nationality|-|-|-|-|-|
   |Drink|-|-|-|-|-|
   |Cigarette|-|-|-|-|-|
   |Pet|-|-|-|**cow**|-|
   |Color|-|-|-|-|-|

**Columnar approach**
Element of ***S*** is filling in an entire column of the grid. For example:

1. statement: "*Person living in house `4` is of `Dutch` nationality, drinks `Whiskey`, smokes a `pipe`, has a pet `cow` and their hosuoe is painted `orange`"

2. grid fill-in:
   | |house #1|house #2|house #3|house #4|house #5|
   |-|---|---|---|---|---|
   |Nationality|-|-|-|Dutch|-|
   |Drink|-|-|-|Whiskey|-|
   |Cigarette|-|-|-|pipe|-|
   |Pet|-|-|-|cow|-|
   |Color|-|-|-|orange|-|

Upside of this approach is that all "identity" constraints are represented by simply selectin one of the allowed combinations. This generally is feasible using the X algorithm.

The downside is that we have now many more elements of ***S***. In the niave approach we needed only as many elements in ***S*** as there were possible attribute values. In the classic puzzle with 5 dimensions (nationality, drink, cigarette, pet, house color), 5 possible values for each dimension and five houses to populate, gave us $|S| = 5 \cdot 5 \cdot 5 = 25$. With the "columnar" approach we have as many elements as there are *combinations* of attributes, and each combination can be assigned to any of the houses, which gives us $|S| = 5 ^5 \cdot 5 = 15 625$

I will discuss later on how we can reduce that number when generating the psarse matrix. Also, during the work on implementing this approach I relised that the "naive" approach can be adapted wo work, giving us the benefit of a small ***S*** while still modelling all cosntraitns properly. That notion will beexpanded upon in a separate project. However, for now, unless otherwise noted, all further work on my solution to the puzzle will be based on using the "columnar" approach.

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