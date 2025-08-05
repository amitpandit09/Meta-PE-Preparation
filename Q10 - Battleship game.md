Battleship game: write a function that finds a ship and return its coordinates.

**Clarifications**

1. Will there be one ship or multiple ? Shall we choose one from random? 
2. What if there is no ship ? 


**Possible ways**

1. Depth-First Search (DFS) – 🔥 Most common
2. Breadth-First Search (BFS)
3. Scan + Inline Expansion


**Algorithm**

**Solution code**

```
import java.util.*;

public class BattleshipFinder {

    public static List<int[]> findFirstShip(char[][] board) {
        int rows = board.length;
        int cols = board[0].length;
        boolean[][] visited = new boolean[rows][cols];

        // DFS helper to collect connected ship parts
        List<int[]> shipCoords = new ArrayList<>();
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (board[r][c] == 'S' && !visited[r][c]) {
                    dfs(board, r, c, visited, shipCoords);
                    return shipCoords;  // Return after first ship is found
                }
            }
        }
        return shipCoords;  // Empty if no ships
    }

    private static void dfs(char[][] board, int r, int c, boolean[][] visited, List<int[]> shipCoords) {
        int rows = board.length;
        int cols = board[0].length;

        if (r < 0 || r >= rows || c < 0 || c >= cols || board[r][c] != 'S' || visited[r][c]) {
            return;
        }

        visited[r][c] = true;
        shipCoords.add(new int[]{r, c});

        // Explore 4 directions
        dfs(board, r + 1, c, visited, shipCoords);  // down
        dfs(board, r - 1, c, visited, shipCoords);  // up
        dfs(board, r, c + 1, visited, shipCoords);  // right
        dfs(board, r, c - 1, visited, shipCoords);  // left
    }

    // Example usage
    public static void main(String[] args) {
        char[][] board = {
            {'.', '.', '.', '.', '.'},
            {'.', 'S', 'S', '.', '.'},
            {'.', '.', '.', '.', '.'},
            {'S', '.', '.', 'S', 'S'},
            {'S', '.', '.', 'S', 'S'}
        };

        List<int[]> ship = findFirstShip(board);
        System.out.println("First ship coordinates:");
        for (int[] coord : ship) {
            System.out.println(Arrays.toString(coord));
        }
    }
}
```

```
