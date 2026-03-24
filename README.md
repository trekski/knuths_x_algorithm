

In this repositry I keep progrmas that I wrote to implement Knuth's X algorithm in order to better understand how the algorithm works. I started by creating a simple Sudoku solver. Then, when I got a better grip on the X algorithm, I attempted applying it to solve the so called [Zebra Puzzles](https://en.wikipedia.org/wiki/Zebra_Puzzle) (a.k.a Einstein's Riddle)

*Repository contents*

Before I get into explaining my approach and results, here's a quick rundown of the contents of the repository, in suggested order of reading:

* `README.md` - this file, explaining how to navigate the repo.
* `zebra_puzzle_v1.md` - a detailed explanaiton of how I went about implementing a solution for the Zebra Puzzle using Knuth's X algorithm.
* `knuth_algo_x_sudoku.ipynb` - a warm-up exercise - implementing Knuth's algorithm X to solve a sudoku
  * `input_n.sudoku` - several examples of sudoku that can be solved by the above program. Coding of a sudoku in this file should be rather self-explanatory
* `knuth_algo_x_einstein.ipynb` - my first attempt to implement a solver for Einstein Puzzles using Knuth's X algorithm
  * `input_n_einstein.yml` - several examples of Einstein puzzles that can be solved using the above program
* `scratcpad` - a  "scratchpad" directory with some w.i.p. versions of some of the code. Can safely be ignored.
 