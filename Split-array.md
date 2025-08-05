Given an array of integers greater than zero, find if it is possible to split it in two subarrays (without reordering the elements), such that the sum of the two subarrays is the same. Print the two subarrays.


**Possible Solutions**
1. Prefix Sum Scan (Optimal - O(n))
2. Prefix Sum Array + Binary Search (O(n))
3. Brute Force Scan (O(n²))

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
