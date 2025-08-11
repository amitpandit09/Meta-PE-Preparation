Given an array of integers greater than zero, find if it is possible to split it in two subarrays (without reordering the elements), such that the sum of the two subarrays is the same. Print the two subarrays.

**Clarification questions**

1. Should we return the first valid split or all possible ones?
2. What should we do if the array has only one or two elements? Are edge cases important?
3. Are the elements always positive integers? Can there be zeros or negatives?
4. Should the two subarrays be contiguous?
5. What should the function return if no split is possible — False, empty list, or an error?


**Approaches**

One-pass prefix scan (best)

Idea: Get total. Walk left→right keeping prefix. If prefix == total - prefix, cut there.

Time: O(n)

Space: O(1)

Notes: Works because all numbers are > 0; returns the first valid split.

Prefix array + (single) binary search

Idea: Build pref[i]. If total is even, binary search for total/2 in pref.

Time: O(n) build + O(log n) search → O(n)

Space: O(n)

Notes: Clean, but uses extra memory.

Prefix array + (per-index) binary search

Idea: For more general checks, binary-search a target for each i.

Time: O(n log n)

Space: O(n)

Notes: Overkill for this exact problem.

Brute force

Idea: Try every cut; recompute left/right sums.

Time: O(n²)

Space: O(1)

Notes: Simple, but too slow.

Two-pointer greedy from ends (optional)

Idea: Grow left/right sums toward middle, always advance the smaller side.

Time: O(n)

Space: O(1)

Caveat: Works with positives, but prefix-scan is simpler and less error-prone.

**Algorithm**

```
1. Calculate the total sum of the array.
2. Iterate through the array while maintaining a running sum (leftSum).
3. At each step, check if leftSum * 2 == total sum. If yes, a split is possible.
4. Split the array at that index and print both subarrays.
```

**Code**

```
import java.io.*;
import java.util.*;

/*
 * To execute Java, please define "static void main" on a class
 * named Solution.
 *
 * If you need more classes, simply define them inline.
 */

class Solution {
  static final int MINE = -1;

  public static void main(String[] args) {
      int[] arr = {1,2,3,3};

      int totalSum = 0;

      for(int num : arr){
        totalSum +=num;
      }  

      if(totalSum%2 != 0){
        return ;
      }

      int halfSum = totalSum/2;
      int prefixSum = 0;
      for(int i=0;i<arr.length-1;i++){
        prefixSum += arr[i];

        if(prefixSum == halfSum){
            System.out.println("\nSub array 1");
            for(int j=0;j<=i;j++){
                System.out.println(arr[j]);
            }

            System.out.println("\nSub array 2");
            for(int j=i+1;j<=arr.length-1;j++){
                System.out.println(arr[j]);
            }
            return;

        }

      }

   System.out.println("\nCannot split");

  }

  

}

```

**Input**

```
int[] arr = {1, 2, 3, 3};

```

**Output**

```
Subarray 1: 1 2 3 
Subarray 2: 3 

```
