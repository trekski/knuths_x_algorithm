# Applying Knuth's X Algorithm to solving zebra puzzles (a.k.a. Einstein's puzzle)

## Abstract

Lead by my long standing interest in puzzles, math and programming, I have over time read a lot about methods of solving various logical puzzles and riddles programatically. This article documents my pet project in which I tried to solve the following problem: Can the so called "zebra puzzles" be modelled and solved using Knuth's X algorithm. The article documents my conceptual work on representing the zebra puzzle conditions as a set of elements (rows) and constraints (columns) in a sparse matrix which is used by the X algorithm. I describe the various challenges I encountered and workarounds I came up with. I believe I got a better understandigg  of Knuth's method alogn the way. For example, conditions which are simple to express in a natural language might need whole systems of matrix rows and columns to encode them. I also document the results of the program I wrote to implement my concept, which proves that the X algortithm is a viable method in this case. Finally, I list ideas for improvements to my apporach that I hope to explore in the next iteration of this project.

## 1. Motivation

### 1.1. History so Far

The motivation behind this project was simply my personal interest in logical puzzles and the wikipedia-rabbit hole I fell into when reading up about the maths of sudoku. I solved many a sudoku in my time and at one point I asked myself the seemingly obvious question: sudokus *can* be solved programmaticaly, right? After all, if not, then how would they be produced in such numbers in different magazines. If so, then *what is the method* to solve a sudoku programatically?

As I was researching that question I happened upon constraint satisfaction problems, Set Cover problems and finally I found out about Knuuth's X algorithm. Then, just to get a better understanding of what I just read, I programmed a simple sudoku solver in Python.

### 1.2. Inspiration

Several months have passed, but the topic must have been somewhere in the back of my head. It only needed a trigger. One day, I happened upon (again) the Einstein puzzle, a.k.a. zebra puzzle. I knew there were many variations on it. And then it clicked. I asked myself the question that lead to this pet project: **Can I solve the zebra puzzle (or in fact any puzzle of that type) using Knuth's algorightm?**

This is the result of trying to answer that question.

### 1.3. Initial Research

My best uninformed bet back then would be: yes, someone must've done it alraedy. But more than the answer itslef I was interested in figuring it out on my own. Finding a method to solve zebra puzzle(s) became a puzzle in its own right. With that in mind I spend ajsut little time actually searching for an answer online. And what I found didn't really ring like the thing I was looking for.
1. There is of course the "brute force" approach[^11]
2. There are mentions[^10][^12] on wikipedia of "algorithms evaluationg the rules" without actually citing any specific source or implementations
3. I've found a "model" written for Alloy[^13]
4. I also found a program written in Ada[^14]

I don't really know Alloy or Ada, but from what I can glean from the shared code, it's more or less like hard-coding the rules set out by the puzzle. Especially for the Ada progam it seems as if in order to solve a different formualtion of the puzzle, the program would have to be re-written from scratch.

I might be wrong of course - I don't really know those languages and might be missing something here. But also, as stated before, it was more about the fun of discovery, than the success of having discovered. It was about the journey!

## 2. Prerequisites

A prerequsite to follow my explanation of how I used the X algorithm to solve zebra puzzles, is to understand the concepts refered to in this section. This section serves only as a reminder, not a full course or in-depth explanation of each of the concepts.

### 2.1. Constraint Satisfaction Problems and Set Cover

A [constraint satisfaction problem](https://en.wikipedia.org/wiki/Constraint_satisfaction_problem) (CSP) is a "mathematical question defined as a set of objects whose state must satisfy a number of constraints or limitations."[^1]. This includes set cover propblems.

A [set cover problem](https://en.wikipedia.org/wiki/Set_cover_problem) can be worded as :
Given a set *U*  (the universe), and a collection $S$ of a given ***m*** subsets of $U$ whose union equals the universe, identify a smallest sub-collection of $S$ whose union equals the universe.

In other words, given a set $U$ (the universe) and a set $S$ (the collection) such that:

$$
\left\\{
\begin{array}{l}
S = \\{x : x \in U \\}\\
\bigcup S = U\end{array}
\right.
$$

Find a sub-collection $`S^*`$ - the smallest subset of the collection, such that $`\bigcup S^* = U`$.

In my understanding from a CSP perspective, in a set cover problem: the objects are the subsets (elements of $S$) and their state of belonging to $`S^*`$  must satisfy the constraint that $`\bigcup S^* = U`$,

A more sctrict formulation is the [exact cover](https://en.wikipedia.org/wiki/Exact_cover) problem, which additionally requires that all elememts of $S^*$ are pairwise disjoint.

Many logical puzzles can be modeled as an exact cover problemn, including but not limited to sudoku, pentomino tiling and n queen problem[^15].

There are known methods to solve these types of problems For example Knuth's X algorithm. Therefore if we are able to map a puzzle or a riddle to an exact cover problem, we can solve such a puzzle using Knuth's X algorithm.

### 2.2. Knuth's X Algorithm and Using a Sparse Matrix

"Algorithm X is an algorithm for solving the exact cover problem. It is a straightforward recursive, nondeterministic, depth-first, backtracking algorithm used by Donald Knuth to demonstrate an efficient implementation called DLX, which uses the dancing links technique."[^5]

To achieve its goal, it works on a sparse matrix that is an incidence matrix of which solution elements satisfy which constraints. That is to say, in the matrix:
- columns represent the constraints / elements of the $U$ universe
- rows represent available options / elements of the $S$ collection (subsets of $U$)
- the matrix is filled with `0` by default except for...
- if a given option/member of $S$ (row) fulfills/contains a given constraint/element of $U$ (column), a `1` is put instead.

Applying Knuth's X algorithm to the sparse matrix solves the CSP it represtents. What we need though is to properly represent the solution elements and the constraints in the matrix.

### 2.3. Sudoku as an Example of a Set Cover Problem

Before we get into the actual goal of this project - solving a zebra puzzle - let's quickly revisit how a sudoku can be solved using Knuth's agorithm. Sudoku can be modelled as an exact cover problem[^4][^4.2] and represetned as a sparse matrix for the X algorithm as follows:

- **elements** of the Universe $U$ are representing specific **constraints** of the sudoku puzzle. These are:
  - constraints on rows, generated by the rule "*each digit can appear only once in each row*":
    - "there is exactly one `1` in the first row"
    - "there is exactly one `2` in the first row"
    - "there is exactly one `3` in the first row"
    - ...
    - "there is exactly one `9` in the ninth row"
  - constraints on columns, generated by the rule "*each digit can appear only once in each column*":
    - "there is exactly one `1` in the first column"
    - "there is exactly one `2` in the first column"
    - "there is exactly one `3` in the first column"
    - ...
    - "there is exactly one `9` in the ninth column"
  - constraints on 3x3 subgrids of the sudoku grid "*each digit can appear one in each 3x3 box*":
    - "there is exactly one `1` in the first 3x3 box"
    - "there is exactly one `2` in the first 3x3 box"
    - "there is exactly one `3` in the first 3x3 box"
    - ...
    - "there is a `9` in the ninth 3x3 box"
  - constraints due to each position in grid being allowed to hold only one digit:
    - "the grid position (1,1) has exactly one digit in it
    - "the grid position (1,2) has exactly one digit in it
    - ...
    - "the grid position (9,9) has has exactly one digit in it
- each **possibility** of putting a number in any of the grid positions represents a fulfillment of **several of the constraints** - i.e. it is a subset of $U$, and hence element of $S$. For example:
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
- each constraint must be fulfilled **exactly once** (if there is a `5` in row *x*, there is exactly **one** `5` in row *x*, etc.)
- additionally we can add explicit constraints from the specific puzzle's text.

Using the above approach a sparse matrix representing a sudoku puzzle can be cosntucted. It is (without specific puzzle text constraints) a $729 \times 324$ matrix that's hard to put whole in this document. A snippet would look like this:

||${\exist !\ "1"\ in\ R4}$|$\exist !\ "1"\ in\ R5$|$\exist !\ "1"\ in\ R6$|...|$\exist !\ "1"\ in\ C2$|$\exist !\ "1"\ in\ C3$|$\exist !\ "1"\ in\ C4$|...|$\exist !\ "1"\ in\ box4$|$\exist !\ "1"\ in\ box5$|
|-|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
||||||||||||
|"1" in R4C2|⬤|◯|◯|...|⬤|◯|◯|...|⬤|◯|
|"1" in R5C2|◯|⬤|◯|...|⬤|◯|◯|...|⬤|◯|
|"1" in R6C2|◯|◯|⬤|...|⬤|◯|◯|...|⬤|◯|
|"1" in R7C2|◯|◯|◯|...|⬤|◯|◯|...|◯|◯|
||||||||||||
|"1" in R6C3|◯|◯|⬤|...|◯|⬤|◯|...|⬤|◯|
||||||||||||
|"1" in  R6C4|◯|◯|⬤|...|◯|◯|⬤|...|◯|⬤|
||||||||||||
||||||||||||

### 2.4 What is a "Zebra Puzzle"?

The "zebra puzzle", alo known as "the Einstein riddle" is a logic puzzle known in many vartations, most notably in the following (attributed to Einstein, hence the name)[^6]:

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

## 3. How to Model the Zebra Puzzle as an Exact Cover Problem

In parallel to sudoku, in order to model the zebra puzzle as a sparse matrix for an exact cover problem, we need to decide:
- what constraints does the puzzle pose - what are the elements of $U$
- how to map elements of the possible solution to subsets of $U$

First we can observe that within the riddle text
- there are mentions of a number people living in the same number of houses. One person lives in exactly one house. Each person has a set of attributes.
- For each person the attriburtes are along the same set of "dimensions" (e.g. each person has a "nationality", each person has a "favourite drink").
- Each dimension  has a range of possible values (e.g. for nationality it's: Norwegian, British, German, Swedish etc.).
- No two persons share the same value along a common dimension . That is: there is one and only one person who's Norwegian, there is one and only one person who drionks milk, etc.
- What follows from all the "one and only one" constraints, is that each person in the final solution needs to have a full set of attributes (Nationality, drink, pet, brand of sigarretes etc.) and a house number where they live.

Commonly[^7][^8][^9] the soluition state is visualised as a grid (*not* yet the Knuth's sparse matrix) that has palces for each house and each *attribute dimension* where *attribute values* are placed, fulfilling the constraints.

**available attributes**
* Nationality : Norwegian
* ~~Nationality : British~~
* Nationality : German
* Nationality : Dutch
* Nationality : Swedish
* Drink : Milk
* ~~Drink : Water~~
* ...

**the grid with a partial solution**
| |house #1|house #2|house #3|house #4|house #5|
|-|---|---|---|---|---|
|Nationality|?|?|British|?|?|
|Drink|?|Water|?|?|?|
|Cigarette|?|?|?|?|?|
|Pet|?|?|?|?|?|
|Color|?|?|?|?|?|

Apart from the "trivial" constraints of the "one and only one" type, we can identify further constraint types in the riddle, as follows:

* **"identity" constraints** - any statement that forces a person living in a house to have two attributes coinciding, not necessarily naming the exact hosue number. These are statements in the form "*The person whose `dimension_a` has value `value_a` also has `value_b` in `dimension_b`*". For example:

  > "*The Spaniard owns the dog*"

  in this case: \
  `dimension_a` = "nationality",\
  `value_a` = "Spanish",\
  `dimension_b` = "pet",\
  `value_b` = "dog"

  and there attributes describe one and the same person.

* **"directed neighbor constraints"** - any statement that forces two people living next to each other to have their attributes coinciding, not naming the exact hosue number. These are statements in the form "*The person whose `dimension_a` has value `value_a` lives on the `direction` of the person whose `dimension_b` has value `value_b`*". For example:

  > "*The green house is immediately to the right of the ivory house.*"

  `dimension_a` = "color",\
  `value_a` = "green",\
  `dimension_b` = "color",\
  `value_b` = "ivory",\
  `direction` = "right"

* **"non-directional neighbor constraints"** - similar to directed neighbor constraints, with the difference that instead of a specific direction ("left" or "right") the constraint is that the two people simply live "next to" each other". For example:

  > "*The Norwegian lives next to the blue house.*"

  `dimension_a` = "nationality",\
  `value_a` = "Norwegian",\
  `dimension_b` = "color",\
  `value_b` = "blue",\
  
  and there attributes describe two persons living next to each other.

### 3.1. Obvious Naive Representation

#### Constraints

Such a visualisation of the problem suggests that:

Elements of *U* could be simply all possible "one and only one" constraints:
  - attribute uniqueness - each attribute is used only once:
    - there is exactly one person with `nationality` = `Norwegian`
    - there is exactly one person with  `nationality` = `English`
    - there is exactly one person with  `nationality` = `Norwegian`
    - ...
    - there is exactly one person with  `pet` = `cat`
    - ...
  - house completeness and unambiguity - each house has a person with exactly one attribute of each of the avaialble dimensions:
    - the person living in house `1`
      - has exactly one `nationality` value assigned
      - has exactly one `drink` value assigned
      - has exactly one `cigarette` value assigned
      - has exactly one `pet` value assigned
      - has exactly one `house color` value assigned
    - the person living in house `2` 
      - has exactly one `nationality` value
      - ...

Let's call these types of constraints "trivial" - any zebra puzzle has to start with at least these constraints. For a classical 5x5x5 zebra puzzle (5 houses, 5 dimensions and 5 values in each dimension) this gives us $ 5 \cdot 5 + 5 \cdot 5 = 50$ columns fot the "trivial" constraints. However,we still need to find a way to represent the "identity" and "neighbor" constraints in the matrix too. This is explored in later sections.

#### Elements of $S$

Elements of $S$ could simply be pairs of attribute and house number. In other a single element would describe a statement like "*The person who's `dimension` has `value` lives in house number `#`*".

* "person with `nationality` = `Norwegian` lives in house `1`"
* "person with `nationality` = `Norwegian` lives in house `2`"
* "person with `nationality` = `Norwegian` lives in house `3`"
* "person with `nationality` = `Norwegian` lives in house `4`"
* "person with `nationality` = `Norwegian` lives in house `5`"
* "person with `nationality` = `British` lives in house `1`"
* ...

For a classical 5x5 Zebra puzzle we have $5 \cdot 5 \cdot 5 = 125$ elements/rows.

### 3.2 First Challenge: "Identity" Constraints

On the face of it, the rerpesentation proposed above fails to be able to rerpesent the "identity" constraints. After all, the representation is designed mostly towards ensuring each attribute is "used" only once and, but identity type constraint requires two different attributes to be "used". In other word, this would be a constraint that is fulfilled not by "one and exactly one" element of *S* but by two elements.

Let's take the statement "*Coffee is drunk in the green house*" for example. Given the proposed $S$, this "constraint" requires us to choose a pair of elements
* either
  * "*`drink` = `coffee` lives in house `1`*"
  * and "*`house color` = `green` lives in house `1`*"
* or 
  * "*`drink` = `coffee` lives in house `2`*" 
  * and "*`house color` = `green` lives in house `2`*"
* or 

  * "*`drink` = `coffee` lives in house `3`*" 
  * and "*`house color` = `green` lives in house `3`*"
* or
  * "*`drink` = `coffee` lives in house `4`*" 
  * and "*`house color` = `green` lives in house `4`*"
* or
  * "*`drink` = `coffee` lives in house `5`*" 
  * and "*`house color` = `green` lives in house `5`*"

Somehow one constraint requires two elements. This means that either we cannot model the zebra puzzle as an exact cover problem, or we have to re-engineer how we construct $U$ or $S% or the incidence matrix.

#### Workaround: Attribute Sets as Solution Elements

A workaround to the above problem I came up with is to construct the elements of $S$ not as single attributes placed in slots of the solution grid, but as full columns of the grid. That is:

**Naive approach**
Element of $S$ placing an attribute in the grid. For example:

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
Element of $S$ is filling in an entire column of the grid. It is a combination of the "naive" elements. For example:

1. statement: "*Person living in house `4` is of `Dutch` nationality, drinks `Whiskey`, smokes a `pipe`, has a pet `cow` and their hosuoe is painted `orange`"

2. grid fill-in:
   | |house #1|house #2|house #3|house #4|house #5|
   |-|---|---|---|---|---|
   |Nationality|-|-|-|Dutch|-|
   |Drink|-|-|-|Whiskey|-|
   |Cigarette|-|-|-|pipe|-|
   |Pet|-|-|-|cow|-|
   |Color|-|-|-|orange|-|

Upside of this approach is that all "identity" constraints are represented by simply selecting one of the allowed combinations. This generally is feasible using the X algorithm.

The downside is that we now have many more elements of $S$. In the niave approach we needed only as many elements in $S$ as there were possible attribute values. In the classic puzzle with 5 dimensions (nationality, drink, cigarette, pet, house color), 5 possible values for each dimension and five houses to populate, gave us $|S| = 5 \cdot 5 \cdot 5 = 125$. With the "columnar" approach we have as many elements as there are *combinations* of attributes, and each combination can be assigned to any of the houses, which gives us $|S| = 5 ^5 \cdot 5 = 15 625$

I will discuss later on how we can reduce that number when generating the sparse matrix. Also, during the work on implementing this approach I relised that the "naive" approach can be adapted wo work, giving us the benefit of a small $S$ while still modelling all constraints properly. That notion will be expanded upon in a separate project. For now however, unless otherwise noted, all further work on my solution to the puzzle will be based on using the "columnar" approach.

NOTE: since we cosntructed the elements of $S$ in this "columnar" approach such that one column already has exactly one attribute from each of the avaialble dimensions, the "trivial" constraints can ignore the "house completness and unambiguity" constraints, leaving us with only **25** "trivial" columns.

### 3.3 Second Challenge: Directed Neighbor Constraints

Now let's turn our attention to the next, as of yet unhandled, type of constraint - the directed neighbor constraint. I chose to describe this one before the non-directional subtype, simply because it is the next one I was able to figure out how to represent in the sparse matrix. Even though it is used only once in the original puzzle text.

The challenge, again, is that we are requiring a coincidence of two attributes. But instead of both of them being for the same person (living in one house), we require the coincidence to happen for two separate people living in neighboring houses. Using the approach from the first workaround would make the number of elements in $S$ explode exponentially. After all, now we'd have to list all the combinations of all possible neighbors. This is not practical by any means.

My intuition told me that we can treat these like a key-lock pair. After all, selecting the attribute placement of one of the neighbors defined by the constraint directly dictates where to place the other attribute. For example, if we consider the following constraint: 

> "*The green house is immediately to the right of the ivory house*" 

We can see that we have the following options:
* The `green` element is house number `2` (regardless of its values along other dimensions), and the `ivory` element is house number `1` (regardless of its values along other dimensions)
* `green` is house number `3` and `ivory` is house number `2`
* `green` is house number `4` and `ivory` is house number `3`
* `green` is house number `5` and `ivory` is house number `4`

We basically have four ways of satisfying the constraint. And in each case the chosen elements of $S^*$ are complimentary. That is: choosing where to put the "green" house directly implies where the "iovry" one has to be.

#### Workaround: Additional Matrix Columns

The "complimentary" nature of the coinciding elements can be understood that selecting one element implies both selecting another element **and** ***not*** selecting several other elements. By playing with this idea and rearranging different sparse matrix fillings, I came to and abstract representation of the neighbor constraint that reflects this concept.

For ease of notation let's use the following notation:
1. If we denote an attribute (dimension and its value) as **A** then **$s_A$** shall denote all elements from $S$ that have that attribute. For example if **A** = "*drink is milk*" then **$s_A$** = set of all elements of $S$ where `drink` = `milk`, regardless of the values in other dimensions
2. Furthermore **$S_{!A}$** denotes all elements from $S$ that **do not** have the attribute **A**.  In the above example, these would be all elements where `drink` is **anything but milk** (coffee, water, etc.) regardless of the values in other dimensions.
3. A special type of attribute is the house number. I will denote it as ***Ni*** (*i* being a number). For example ***N2*** denotes "*a person living in house number `2`*"
4. I denote the intersection of any **$s_A$** and **$s_A$** as  **$S_{[A,B]}$**. In other words $S_{[A,B]}=S_{[B,A]}=s_A \cap s_B$

**Proposal**

My proposition is that we can represent the puzzle constraint expressed in natural language as "*Person with attribute `A` lives left/right of person with attribute `B`*" as a *set* of columns - each corresponding to one of the possible neighbor arrangements in space.

Following the example 

> "*The green house is immediately to the right of the ivory house*" 

We can assign the attributes as:

* A = "`house color` is `green`"
* B = "`house color` is `ivory`"

NOTE: in this case both A and B share the same dimension, but that does not need to always be the case

Given the notation proposed above and the assignments, for example $S_{A,!B,N4}$ means "*all elements of **S** where `house color` is `green` and `house` color is not `ivory` and house number is `4`*" (all the while pet, cigarette, nationality and drink are of any avaiallbe value).

As proposed above, the "neighbor" constraint can be represented in the sparse matrix as a collection of atomic constraint columns implementing the key-lock approach. Using the suggested notation Using this notation this can be written as follows:
* with 5 houses, there are 5 options to place thetwo neighbors.
* we add an additional atomic constraint column representing each of the options of placing the first neighbor to the matrix.
* all elements of $S_{A,!B,Ni}$ have cells filled in constraint columns for option *i* (the key)
* all elements of $S_{!A,B,Ni}$ have cells filled in constraint columns for all options ***but*** *i* (the complementary lock)
* any remaining elements of $S$ are not matched to those new columns

||option 1|option 2|option 3|option 4|
|-|:-:|:-:|:-:|:-:|
|$S_{A,!B,N2}$|⬤|◯|◯|◯|
|$S_{A,!B,N3}$|◯|⬤|◯|◯|
|$S_{A,!B,N4}$|◯|◯|⬤|◯|
|$S_{A,!B,N5}$|◯|◯|◯|⬤|
||||||
|$S_{!A,B,N1}$|◯|⬤|⬤|⬤|
|$S_{!A,B,N2}$|⬤|◯|⬤|⬤|
|$S_{!A,B,N3}$|⬤|⬤|◯|⬤|
|$S_{!A,B,N4}$|⬤|⬤|⬤|◯|
||||||
|other<sup>*</sup>|◯|◯|◯|◯|

<sup>*</sup>) *other = any elements not listed explicitly above, i.e. $`S_{!A,!B}`$ and $`S_{A,B}`$ (if they exist)*


**Comparison to natural language**

Each of the "options" is hard to represent in natural language. Especially that only considered together do they express the more "natural" concept of "*`A` is left/right of `B`*". Nevertheless they could be translated into something like:
* option 1 = "*second house is green OR the ivory house is in not in position 1*"
* option 2 = "*third house is green OR the ivory house is in not in position 2*"
* option 3 = "*fourth house is OR the ivory house is in not in position 3*"
* option 4 = "*fifth house is OR the ivory house is in not in position 4*"

**Completeness**

Note how:
* Elements with complimentary attributes from sets $S_{A,!B}$ and $S_{!A,B}$ fullfill the new atomic constraints, but elements from $S_{A,B}$ do not - this is because the two people are neighbors, not the same person. One person  having one of the attributes exlcudes that person from having the other attribute mentioned in the constraint.
* House indexes for the complimentary attributes are shifted by one to the right compared to the indexes for the other attribute . When for "house is green" we have $S_{A,!B,Ni}$ where $i = 2...5$, then for "house color is ivory" we have $S_{!A,B,Ni}$ where $i = 1...4$. This is because the neighbors' relative positions are exactly defined.
* It is impossible to satisfy all four options just by selecting one of each of the $S_{A,!B,Ni}$ elements. This is because choosing any one of them precludes choosing any other of them due of the "trivial" constraints mentioned earlier. Choosing an element from $S_{A,B,Ni}$ amounts to choosing an element from $S_A$.
  * for example choosing any of $S_{A,!B,N2}$ ("*second hosue is green*") makes choosing any of $S_{A,!B,N3}$ through $S_{A,!B,N5}$ impossible, because no other hosue can be green anymore in this situation.
* The representation is complete. The only way to cover all of the options that constitute the puzzle text we need to pick exactly one of the $S_{A,!B}$ items and one of the $S_{!A,B}$ items, so that their indexes are in the correct order (one is right of the other). 

**Omitting invalid assignments**

Also note that we skip invalid indexes when filling in the sparse matrix. This is specifically to  avoid an edge case, where after applying the index shifting, one element of $S$ covers all options. Specifically in our example any member of $S_{!A,B,N5}$ would fulfill all four options. If we cosntruct this case by extension from the already mentioned ones we get:

$S_{!A,B,N5}$ = "`ivory` is the color of the house number `5`"

||option 1|option 2|option 3|option 4|
|-|:-:|:-:|:-:|:-:|
|$S_{!A,B,N5}$|⬤|⬤|⬤|⬤|

And any element from $S_{!A,B,N5}$ would satisfy all atomic options, without there ever being an eighbor chosen. Thus we have to skip the edge case when filling in the matrix.

### 3.4. Third challenge: Non-directed Neighbor Constraints

Finally the non-directed neighbors constraints. Initially they look similar to the previous ones. With one key difference: selecting one of the neighbors does not unambiguously let us determine the other neighboring attribute. Let's consider the following example:

> "*The **Norwegian** lives next to the **blue** house*"

Let's assign the attributes as:

* A = "`nationality` is `Norwegian`"
* B = "`house color` is `blue`"

This time we can see that selecting where to place the first attribute sometimes leaves us with two possible placements for the second attribute:
* `Norwegian` is in house number `1` (regardless of its values along other dimensions), and `blue` is house number `2` (regardless of its values along other dimensions), BUT
* `Norwegian` is in house number `2` and
   - either `blue` is house number `1`
   - or `blue` is house number `3`
* `Norwegian` is in house number `3` and
   - either `blue` is house number `2`
   - or `blue` is house number `4`
* `Norwegian` is in house number `4` and
   - either `blue` is house number `3`
   - or `blue` is house number `2`
* `Norwegian` is in house number `5` and `blue` is house number `4`

We need to be able to somehow select one of the options and then (if needed) its "sub-option". 

#### Workaround: Even More Additional Matrix Columns and Rows

Note: all sub-options from the above list can be grouped pairwise and rearranged as follows:
* the two neightobrs in question live in houses `1` and `2`
  * either `Norwegian` lives **left** (1) and `blue` is **right** (2)
  * or `Norwegian` lives **right** (2) and `blue` is **left** (1)
* the two neightobrs in question live in houses `2` and `3`
  * either `Norwegian` lives **left** (2) and `blue` is **right** (3)
  * or `Norwegian` lives **right** (3) and `blue` is **left** (2)
* ...

**Proposal**

Building on the previously proposed key-lock arrangement of the sparse matrix I propose a solution in which we are able to select one of the new super-options by adding new synthetic rows to the matrix. Each of those rows would represent placing the two neightbors in a given place overall. Or rather: key in our possible selection of exact neighbors. Then, the selection of the actual attribute placement would follow by complimenting that pre-selection with selection of each of the neighbors.

To make itmore tnagible, I propose that:
* we add an additional atomic constraint column for each of the options of placing any of the two neighbors.
* we add additional elements in $S$ representing the possible arrangements of the pair of neighbors in adjacent positions *i* and *i+1*.
* Each such element has the matrix filled in for columns representing options *i* and *i+1* (the lock)
* elements of $S_{A,!B,Ni}$ have cells filled in constraint columns for option *i* (first part of the key)
* elements of $S_{!A,B,Ni}$ have cells filled in constraint columns for option *i* (second part of the key)
* any remaining other elements are not matched

To visualise the above in the sparse matrix:

||option 1|option 2|option 3|option 4|option 5|
|-|:-:|:-:|:-:|:-:|:-:|
|one neighbor is from $S_{N1}$ <br /> and the other from $S_{N2}$|◯|◯|⬤|⬤|⬤|
|$S_{N2}$ and $S_{N3}$|⬤|◯|◯|⬤|⬤|
|$S_{N3}$ and $S_{N4}$|⬤|⬤|◯|◯|⬤|
|$S_{N4}$ and $S_{N5}$|⬤|⬤|⬤|◯|◯|
|||||||
|$S_{A,!B,N1}$|⬤|◯|◯|◯|◯|
|$S_{A,!B,N2}$|◯|⬤|◯|◯|◯|
|$S_{A,!B,N3}$|◯|◯|⬤|◯|◯|
|$S_{A,!B,N4}$|◯|◯|◯|⬤|◯|
|$S_{A,!B,N5}$|◯|◯|◯|◯|⬤|
|||||||
|$S_{!A,B,N1}$|⬤|◯|◯|◯|◯|
|$S_{!A,B,N2}$|◯|⬤|◯|◯|◯|
|$S_{!A,B,N3}$|◯|◯|⬤|◯|◯|
|$S_{!A,B,N4}$|◯|◯|◯|⬤|◯|
|$S_{!A,B,N5}$|◯|◯|◯|◯|⬤|
|||||||
|other|◯|◯|◯|◯|◯|

**Completeness**

Similarily to above points about directed neighbors:
* no two rows from the $S_{!A,B,Ni}$ group can be chosen due to "trivial" constraints
* no two rows from the $S_{A,!B,Ni}$ group can be chosen due to "trivial" constraints
* to cover all options (atomic constraints) we need to select exactly three rows:
  1. one of the "*neighbors are in $S_{Ni}$ and $S_{Nj}$*" elements - the keying element,
  2. one of the items to be the first neighbor - in whichever of the two possible places,
  3. another item to be the other neighbor - occupying the remainig place.

**Other notes**

Just ass with the directed neighbors, the elemets to be selected are described by by using complimentary key-lock entires in the matrix, albeit with a more complex structure. This representation is even harder to translate back into natural language, but by now hopefully you can see how the puzzle constraints expressed in natural language can be modelled by more elaborate combinations of rows and columns in the sparse matrix, all the while preserving the constraint's "logic".

As with the directed neighbors, care needs to be taken to omit invalid combinations of house indexes when cosntructing the synthetic rows and atomic constraint columns.

## 4. Implementation Considerations

After defining the puzzle, how to model it as an exact cover problem and how it can be represented in the X algorithm's sparse matrix, we need to consider a few practical details of how to actually implement it in code. First and foremost:
- how to effectively generate the rows representing elements of $S$
- how to effectively crgenerateeate the columns representing the constraints
- how to match the rows against the appropriate columns (or vice versa)

### 4.1. Number of Combinations and "Row Culling"

As mentioned in 2.2, if we switch from a "naive" representation ($S$ elements are tiles on the solution "grid") to a "columnar" representation ($S$ elements are full columns of the solution grid) we increase the collection cardinality dramatically. For the classic 5x5x5 puzzle it goes from 125 to ~15k. This is because we have to generate all possible combinations of all the dimension values.

However, we can dramatically reduce that number already upon row generation. Consider that to generate all combinations of all possible values in each dimension we would simply execute a nested loop through each dimension in sequence:
  1. looop through all values of dimension 1. While doing that, in each itermation...
  2. looop through all values of dimension 2. While doing that, in each itermation...
  3. looop through all values of dimension 3. While doing that, in each itermation...
  4. ...
And on the deepest level of this nested loop a column is build by combining all values from each of the dimensions

BUT

While looping, we can check whether or not any of the partial attribute sets are invalid and skip step(s) in the loop if needed.  Let's consider those nested loops as a depth-first search of a tree of all possible combinations of dimension values. For the sake of an example let's assume:
- we are at the beginning of the nesting
- first dimension is `nationality`
- second dimension is `drink`
And let's consider how the constraint "*The Ukrainian drinks tea*" would affect loop execution.

Given puzzle text we know that: 
- any element where `nationality` = `Ukranian` cannot have a `drink` other than `tea`
- and conversly any element where `drink` =`tea` cannot have `nationality` be `Ukranian`

This let's us skip loop steps whenever we detect any invalid comnbination:
- when we are on the "Ukrainian" element, we will skip all drink elements except for "tea"
- when on any other nationality element, we will skip the "tea" drink elments

```mermaid
flowchart LR
    classDef stop stroke:#f00;
    start@{ shape: circle, label: "Start" }
    B1_loop([nationality : English])
    B2_loop([nationality : Spanish])
    B3_loop([nationality : Ukrainian])
    B4_loop([nationality : Norwegian])
    B5_loop([nationality : Japanese])
    C1_1([drink : milk])
    C1_2([drink : tea])
    C1_3([drink : coffee])
    C1_4([drink : juice])
    C1_5([drink : water])
    C2_1([drink : milk])
    C2_2([drink : tea])
    C2_3([drink : coffee])
    C2_4([remaining drinks...])
    C3_1([drink : milk])
    C3_2([drink : tea])
    C3_3([drink : coffee])
    C3_4([drink : juice])
    C3_5([drink : water])
    start --> A[[loop through nationalities]]
    A --> B1_loop --> B1[[loop through drinks]]
    A --> B2_loop --> B2[[loop through drinks]]
    A --> B3_loop --> B3[[loop through drinks]]
    A --> B4_loop --> B4[[loop through drinks...]] ~~~ C3_5
    A --> B5_loop --> B5[[loop through drinks...]] ~~~ C3_5
    B1 --> C1_1 --> D1_1[[loop through dim 3...]]
    B1 --> C1_2 --> D1_2(((STOP))):::stop
    B1 --> C1_3 --> D1_3[[loop through dim 3...]]
    B1 --> C1_4 --> D1_4[[loop through dim 3...]]
    B1 --> C1_5 --> D1_5[[loop through dim 3...]]
    B2 --> C2_1 --> D2_1[[loop through dim 3...]]
    B2 --> C2_2 --> D2_2(((STOP))):::stop
    B2 --> C2_3 --> D2_3[[loop through dim 3...]]
    B2 --> C2_4
    B3 --> C3_1 --> D3_1(((STOP))):::stop
    B3 --> C3_2 --> D3_2[[loop through dim 3...]]
    B3 --> C3_3 --> D3_3(((STOP))):::stop
    B3 --> C3_4 --> D3_4(((STOP))):::stop
    B3 --> C3_5 --> D3_5(((STOP))):::stop
```

In the tested example the approach of "culling" invalid elements reduced the size of $S$ by two orders of magnitude to around 300 entries. 

### 4.2. Lookup and Filling in the Sparse Matrix

At this point, assuming row culling is performed, there are about 300 rows and 25 columns for the "trivial" constraints. Additionally, as mentioned in 2.2 and 2.3, to properly represent the two different types of neighbor constraints, several addtional constraint columns arwe needed. For a puzzle with *n* houses we need *n-1* additional columns for each directed neighbor constraint and *n* for each undirected neighbor constraint. We also need *n* additional "keying" rows for each undirected neighbor constraint.

Having generated all the rows and all the columns we ned to fill in the matrix. To facilitate the process, it would be best to avoid a situation in which each of the rows is compared against each of the  columns to check if they should be connected. So, for easier lookup of which rows should be connected to which columns, I constructed a lookup mechanism.

Specifically:
- on one hand all rows have their defining attributes assigned to them
  - for "trivial" rows it simply all five attributes of each element
  - for all extra rows for undirected neighbors it's the two attributes (one for each neigbor) and the position in which the pair could be placed
- on the other hand, each column is indexed in dictionaries matching their characteristic upon genration
  - for each "trivial" column it's the column's respective attribute and house position
  - for the addtional neighbor columsn it's the attributes of each neighbor pairing and the position they relate to

Because of how the matrix is filled in, even with these lookup dictionaries for columns it still resutls in quite many loops. Consider an example of assigning the "trivial" elemetns of $S$ to the directed neighbor columns:

1. Let's consider the classical 5x5x5 puzzle
2. Let's consider a neighbor constraint in the form "*Person with attribute `A` lives left of the person with attribute `B`*"
2. Fourd extra columns are needed for the key-lock representation, as discussed in 2.2
   - these colums can be punt in a dictionary addressed by the two neighbor attributes and the house number. Let's call this dictionary `d`. The columns are indexed somewhere in `d[A][B][i]` for `i = 1 .. 4`
3. All rows belonging to $S_{A,!B, Ni}$ need to be matched to a column with the same `i`:
   - we look up `A` in `d
    - we ***loop*** through all entires `x` in `d[A]` and discard ones where `x == B`
       - for each entry `x` we take d[A][x][i]
4. All rows belonging to $S_{!A,B, Ni}$ need to be matched to a column with the same `i`:
   - we look up `B` in `d`
    - we ***loop*** through all entires `y` in `d[B]` and discard ones where `x == A`
      - we ***loop*** through all entires `j` in `d[B][x]` and discard ones where `j == i`

Given that each "trivial" element of $S$ hast 5 attributes, we would have to repeat that process $\binom{5}{2} = 10$ times for each element.

## 5. Observations

I have ran the final script against a few different puzzle formulations:
* [input_1_einstein.yml](input_1_einstein.yml) - "Who's got a Fish?" riddle found in [Polish wikipedia](https://pl.wikipedia.org/wiki/Zagadka_Einsteina#Jedno_z_mo%C5%BCliwych_sformu%C5%82owa%C5%84) (accessed: 2026-01-12, translated by me)
* [input_2_einstein.yml](input_2_einstein.yml) - "Who's got a Zebra?" riddle found in [English wikipedia](https://en.wikipedia.org/wiki/Zebra_puzzle#Description) (accessed: 2026-03-07)
* [input_3_einstein.yml](input_3_einstein.yml) - "Ships" riddle found on [brainden.com](http://brainden.com/einsteins-riddles.htm) (accessed: 2026-03-08, **NOTE** site does not support https)

I ran the program on 6 core AMD Ryzen 5 7640U with 32GM memory. For each of the riddles the program (with culling of trivial rows) was able to find the correct solution well within <1s. When run without row culling the "fish" riddle did not finish within 90 minutes, after which I aborted further experiments.

### 5.1. Solution Times

Because of how YML is read, the dimensions, attributes and contraitns are added to the puzzle definition in different order each time the puzzle is ran. So the times presented here are averages of 10 runs for runs.

|riddle|avg. backtracks|avg. setup<sup>1</sup> time|avg. search<sup>2</sup> time|avg. total<sup>3</sup> time|
|-|-|-|-|-|
|"fish"|2.10 ± 0.99|111 ± 24|8.2 ± 1.7|154 ± 31|
|"zebra"|2.8 ± 2.0|102 ± 17|8.7 ± 2.2|142 ± 24|
|"ships"|3.70 ± 0.95|102.7 ± 5.2|12.1 ± 2.0|147.0 ± 7.6|

<sup>1</sup>) time to generate the matrix columns and rows (with culling) and match them \
<sup>2</sup>) time to find the solution once the matrix is ready \
<sup>3</sup>) total time from start of program to output of the solution

### 5.2. More Advanced Puzzles

When looking for more examples of the Zebra puzzle, I found several puzzles which extended the basic idea in new ways. Ways which included modifications on the alredy idenfitied types of constraints.

* Puzzles where except for immediate neighbors (directed or non-directed) the neighbors can be also separated by an arbitrary number of other entities. For example this
  [hard Daily Zebra puzzle #841](https://www.zebrapuzzles.com/p/2XJRpQtw/#hard)(accessed: 2026-03-25)
  contains the following constraint:
  > "*The influencer who has been to Thailand is **somewhere to the right** of the woman wearing a Pink shirt.*"

  None of the methods to encode "neighbor" relationships proposed above can handle such a loose case at this moment.
* Puzzles where there is no order imposed on the entities. For example the "Meeting" puzzle found on [brainden.com](http://brainden.com/einsteins-riddles.htm). In this puzzle none of the clues mention any kind of order or placement of the entities in space. Both the "naive" and "columnar" representations proposed above implicitly assumed there to be a dedicated spatial dimension - order of the entities. In this case the puzzle simply cannot be represented in any meaningful way. If we still require there to be ordered places for thee entities, the entities would have to be allowed to be in any of the positions without any constraints. This would dramatically expand the number of possible combinations and the size of the search tree.

The new constraint types introduced by those puzzles are not covered by the model porposed so far. This means that new approaches need to be found to solve these puzzles.

## 6. Conclusion and Next Steps

### 6.1. Validity of the Solution

As is demonstrated with the implemented code, it is possible to model the zebra puzzle as an exact cover problem and solve it using Knuth's X algorithm. The solution runtimes for the sparse matrix itself are about 8 to 2 ms. This roughly approaches the benchmark times cited in Wikipedia: "*The execution time of a program [which implements the elgorithm for evaluating rules in an optimized order] written in Scala running on a 3 GHz processor is approximately 1 millisecond.*"[^10]

### 6.2. New concepts

As for the sparse matrix representation of the Zebra Puzzle, most notably I've learned that:
- Sometimes a constraint that is easy to express in a natural language, might need to be translated into a system of interdependent rows and columns in the matrix. I called it here the "key and lock" approach and used it to express the "neighbor" constraints.
- In a way, there can be a tradeoff between more elaborate elements in $S$ and having more constraints. Consider how using the "columnar" approach lets us drop some of the constraint columns in the matrix at the expense of generating many more rows.

### 6.3. Areas for Improvement

On the other hand however, the workarounds I have implemented have some drawbacks:

- using the "columnar" approach also meant that cardinality of $S$ increased exponentially.
- Following this increase, the algorithm runs many orders of magnitude slower.
- To counteract that I had to implement "row culling" so the matrix size and search tree size don't blow up
- Another drawback of the columnar approach was the amount of nested loops that needed to be perfomred to properly fill in the matrix. This, agian, is a good candidate for optimisation in the program.

And finally, current approach does not let me model the more complex puzzles of this type. Ones where the neighbor constratints are even more vague or where there is no "order" between the entities.

### 6.4 Going Back to the "Naive" Approach

Having worked long enough on how to represent the directed and non-directed neighbor constraints in the matrix, I reliazed that the "identity" constraints are nothing but another flavor of a "neighbor" constraint. After all a condition like:

> "*`Kools` are smoked in the `yellow` house.*"

Can be rewritten as

> "*The person who smokes `Kools` and the person who lives in the `yellow` house are one person who `lives in the one house`*"

After pouring time and heart into following my inital idea for the "columnar" approach, which both helped and hindered me at times, I must acknowledge there is a simpler way. A full circle moment.

[^1]: https://en.wikipedia.org/wiki/Constraint_satisfaction_problem
[^2]: https://en.wikipedia.org/wiki/Set_cover_problem#Related_problems
[^3]: https://en.wikipedia.org/wiki/Exact_cover
[^4]: https://en.wikipedia.org/wiki/sudoku_solving_algorithms#Constraint_programming
[^4.2]: https://en.wikipedia.org/wiki/Exact_cover#sudoku
[^5]: https://en.wikipedia.org/wiki/Knuth%27s_Algorithm_X
[^6]: https://en.wikipedia.org/wiki/Zebra_puzzle
[^7]: https://www.wikihow.com/Einstein%27s-riddle
[^8]: https://medium.com/brainzilla/einsteins-riddle-step-by-step-tutorial-on-how-to-solve-the-world-s-hardest-puzzle-46bcf054a7c7
[^9]: https://youtu.be/HaNSHQOrSX8?t=1255
[^10]: https://pl.wikipedia.org/wiki/Zagadka_Einsteina#Algorytm_ewaluuj%C4%85cy_regu%C5%82y_w_zoptymalizowanej_kolejno%C5%9Bci
[^11]: https://pl.wikipedia.org/wiki/Zagadka_Einsteina#Algorytm_brute_force
[^12]: https://pl.wikipedia.org/wiki/Zagadka_Einsteina#Algorytm_ewaluuj%C4%85cy_regu%C5%82y
[^13]: https://github.com/AlloyTools/models/blob/master/puzzles/einstein/einstein-wikipedia.als
[^14]: https://rosettacode.org/wiki/Zebra_puzzle
[^15]: https://en.wikipedia.org/wiki/Exact_cover#Noteworthy_examples