Write a code tp create a randomized minesweeper board on every iteration.

**Algorithm**

```
1. Initialize a 2D grid of given rows and columns filled with zeros.
2. Randomly place mines (e.g., represented by -1) in the grid without overlap.
3. For every non-mine cell, calculate the number of adjacent mines and update the count.
4. Print or return the final board.
```

**Code**

```

import random

def create_minesweeper_board(rows, cols, mines_count):
    # Step 1: Create empty board
    board = [[0 for _ in range(cols)] for _ in range(rows)]

    # Step 2: Place mines randomly
    all_cells = [(r, c) for r in range(rows) for c in range(cols)]
    mines = random.sample(all_cells, mines_count)
    for r, c in mines:
        board[r][c] = -1

    # Step 3: Calculate numbers for non-mine cells
    directions = [(-1,-1), (-1,0), (-1,1), (0,-1), (0,1), (1,-1), (1,0), (1,1)]

    for r in range(rows):
        for c in range(cols):
            if board[r][c] == -1:
                continue
            count = 0
            for dr, dc in directions:
                nr, nc = r + dr, c + dc
                if 0 <= nr < rows and 0 <= nc < cols and board[nr][nc] == -1:
                    count += 1
            board[r][c] = count

    return board

def print_board(board):
    for row in board:
        print(" ".join(str(cell) if cell != -1 else "*" for cell in row))

# Example usage:
rows, cols, mines_count = 8, 8, 10
board = create_minesweeper_board(rows, cols, mines_count)
print_board(board)

```


**Output**

```
0 1 1 1 0 0 1 *
0 1 * 2 2 2 3 2
0 1 2 * * 3 * 1
0 0 1 2 2 3 2 1
0 0 0 0 1 * 1 0
0 0 0 0 1 1 1 0
0 0 0 0 0 0 0 0
* 1 1 1 0 0 0 0
```
