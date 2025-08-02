Write a code tp create a randomized minesweeper board on every iteration.

**Algorithm**

```
Create a board of size n x m.

Place k mines randomly.

Calculate the number of adjacent mines for each non-mine cell.

Print the final board.
```

**Code**

```

import java.util.*;

public class RandomMinesweeper {

    static final int MINE = -1;

    public static void main(String[] args) {
        int rows = 5;
        int cols = 5;
        int mines = 5;

        int[][] board = generateBoard(rows, cols, mines);
        printBoard(board);
    }

    static int[][] generateBoard(int rows, int cols, int mineCount) {
        int[][] board = new int[rows][cols];

        // Step 1: Place mines randomly
        Random rand = new Random();
        int placed = 0;

        while (placed < mineCount) {
            int r = rand.nextInt(rows);
            int c = rand.nextInt(cols);
            if (board[r][c] != MINE) {
                board[r][c] = MINE;
                placed++;
            }
        }

        // Step 2: Fill numbers
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (board[r][c] == MINE) continue;

                int count = 0;
                for (int dr = -1; dr <= 1; dr++) {
                    for (int dc = -1; dc <= 1; dc++) {
                        int nr = r + dr;
                        int nc = c + dc;
                        if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && board[nr][nc] == MINE) {
                            count++;
                        }
                    }
                }
                board[r][c] = count;
            }
        }

        return board;
    }

    static void printBoard(int[][] board) {
        for (int[] row : board) {
            for (int cell : row) {
                if (cell == MINE) {
                    System.out.print("* ");
                } else {
                    System.out.print(cell + " ");
                }
            }
            System.out.println();
        }
    }
}


```


**Output**

```
0 1 * 2 1 
0 1 2 * 1 
0 0 2 2 2 
0 0 1 * 2 
0 0 1 1 1 

```
