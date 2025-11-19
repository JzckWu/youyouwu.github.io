# Backtracking

1. **game tree** of two players: two players take turns to move, either 1 step or jump one block, first to reach all three into last column/row wins.  A good state is at which you won or any move the other makes will make you win; bad are other states, namely you lose or any moves you make make the other wins. ** In fact this can be extended to any two player game with no randomness**  ![image-20251108153112810](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251108153112810.png)

    

2. **text segmentation** 

    given string A[1..n] to segment, and a function IsWord where bool of whether w is a world. Can we partition. Time complexitiy:  O(2^n)

    ```bash
    splitable(A[1...n]):
    if n == 0
    	return true
    for i 1 to n
    	if isworld A[1...i]
    		if splitable(A[i+1...n])
    			return true
    return false
    ```

    ```bash
    splittable(i):
    if i > n
    	return true
    for j i to n
    	if isword(i, j)
    	# true if A[i...j] is a word
            if splittable(j+1)
            # true if A[j+1...n] is the concatenation of words
            return true
     return false
    	#same as above
    ```

    ![image-20251108160504700](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251108160504700.png)

    

## Speedup method

Use A[1...n] as global variable, pass the start index i of a suffix for recursion

# Fibonacci

use array to cache. O(n).

iterative fibo, use prev and curr, updating, use O n^2 time

use a matrix to speed up to O logn

## Time complexity ana

Ignore the computation of sub problems, look at the pseudo code of the problem, then time the number of subproblems