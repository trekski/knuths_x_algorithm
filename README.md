

In this repositry I keep progrmas that I wrote to implement Knuth's X algorithm in order to better understand how the algorithm works. I started by creating a simple Sudoku solver. Then, when I got a better grip on the X algorithm, I attempted applying it to solve the so called [Zebra Puzzles](https://en.wikipedia.org/wiki/Zebra_Puzzle) (a.k.a Einstein's Riddle)

*Repository contents*

Here's a quick rundown of the contents of the repository, in suggested order of reading:

* [README.md](README.md) - this file, explaining how to navigate the repo.
* [zebra_puzzle_v1.md](zebra_puzzle_v1.md) - a detailed explanaiton of how I went about implementing a solution for the Zebra Puzzle using Knuth's X algorithm.
* [knuth_algo_x_sudoku.ipynb](knuth_algo_x_sudoku.ipynb) - a warm-up exercise - implementing Knuth's algorithm X to solve a sudoku
  * [input_n.sudoku](https://github.com/search?q=repo%3Atrekski%2Fknuths_x_algorithm%20path%3A*.sudoku&type=code) - several examples of sudoku that can be solved by the above program. Coding of a sudoku in this file should be rather self-explanatory
* [knuth_algo_x_einstein.ipynb](knuth_algo_x_einstein.ipynb) - my first attempt to implement a solver for Einstein Puzzles using Knuth's X algorithm
  * [input_n_einstein.yml](https://github.com/search?q=repo%3Atrekski%2Fknuths_x_algorithm%20path%3A*einstein.yml&type=code) - several examples of Einstein puzzles that can be solved using the above program
* [scratchpad](scratchpad) - a  "scratchpad" directory with some w.i.p. versions of some of the code. Can safely be ignored.
 