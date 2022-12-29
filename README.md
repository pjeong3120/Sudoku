# Sudoku Solver
This script was written to solve classic sudoku puzzles by applying the algorithms that are implicit in the rules of Sudoku so that combinatorial complexity growth due to "guess and check" can be avoided. Currently, it can only solve 9x9. However, the algorithms can be generalized to sudoku puzzles of n dimensional hypercubes (2D squares, 3D cubes, 4D tesseract, etc.) as long as the length of the cube is some integer to the nth power (4x4 squares, 8x8x8 cubes, 16x16x16x16 tesseract, etc.). With further tweaking, these algorithms can also be applied to other variants like Killer Sudoku.

## 1. Data Structures:

Each 9x9 sudoku puzzle is represented by a Classic_Grid object. Each Grid object holds its Container objects and its Entry objects. The Container object represents the rows, columns, and subgrids (the 3x3 squares) of the 9x9 Grid, thus the Classic Grid will have 9 rows, 9 columns, 9 subgrids, and 27 total Container obejcts. Each entry is the singular unit of the sudoku puzzle. Each Grid will contain 81 Entry objects and each Container object will contain 9 Entry objects, not necessarily distinct.

In order to construct a Grid, simply initialize a Grid object with a 2D 9x9 list passed in. For unsolved entries, use 0. Several examples are included at the bottom of the Jupyter notebook called Solver.ipynb. Other useful methods for Grid objects include Grid.visualize(), which will visualize the current state of the Grid, and Grid.solve(), which will solve the puzzle.

### a) Classic_Grid:
#### i) dims: Keeps track of the length of the cube; Note that for each container, exactly dims entries are mapped to dims distinct integers.
#### ii) containers: A dictionary with (key, value) pairs := (String to represent what type of container ("rows", "cols", "subgrids", "all"), list of container of that type)
#### iii) entries: This is just the input but instead of integers, the elements of the 2D list are Entry objects; allows for easy access of Entries

### b) Classic_Container:
#### i) grid: Pointer back to containing Grid object
#### ii) entries: List of all contained Entry objects
#### iii) solved_vals: List of all value mappings; mappings[i] points to the mapped entry (that the i is mapped to) if solved and None otherwise
#### iv) mappings: List of lists; mappings[i] points to a list of possible entries if i is unsolved and an empty set otherwise; 1-Indexed for simplicity

### c) Classic Entry:
#### i) position: (row, col)
#### ii) grid: Pointer to containing grid
#### iii) row, col, subgrid: Pointer to Container objects
#### iv) containers: List containing row, col, subgrid; useful for iteration
#### v) val: Value of entre; 0 if unsolved and a positive integer if solved
#### vi) solved: Boolean; self explanatory
#### vii) mappings: List of potential value mappings for entry

## Algorithms Overview:
The algorithm can be broken down into three parts, in increasing computational cost. During alg1, if any changes are made, alg1 will be run again so that as much progress can be made before more expensive steps are taken. Similarly, if any changes are made during alg2, alg1 will run, then alg2 will run so that progress is maximized before the very expensive alg3 step is taken. The same applies for alg3 (alg1 is run, then alg2, then alg3) 
### alg1:
If an entry has only one possible value that it can be mapped to, then we solve that entry.

### alg2:
If a container has only one possible entry that a value can be mapped to, then that entry must be mapped to that value.

### alg3: Pigeonholes
First, consider the following example: Entry A can be mapped to (1, 2), Entry B can be mapped to (1, 2), and Entry C can be mapped to (1, 2, 3, 4). Note that Entry C cannot be 1 or 2; otherwise Entry A and Entry B must both be 1 or both be 2. Effectively, Entry C can only be mapped to (3, 4) due to the fact that there is some bipartite matching between (A, B) and (1, 2). 

If a set of n entries can be mapped to a set of exactly n values, then there must be a bipartite matching between the entry set and the values set. In other words, let the entries be pigeonholes and let the values be pigeons.

This step is most computationally expensive for the following reason: the algorithm considers every subset of unsolved entries for a container and then checks if the union of that subset's possible value mappings is equal in length. In other words, the algorithm computes the power-set of the unsolved entries, then iterates through the power-set; each element of the power-set is a distinct subset of the unsolved entries, and using this subset, we take the union of each entry's possible mappings. If this is equal to the length of the subset, then this means that there are n possible mappings for n possible entries. A value can only be mapped to an entry if and only if both or none of the entry and value are elements of the union of value mappings and the subset of unsolved entries.



