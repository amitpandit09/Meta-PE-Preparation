Given an array of integers greater than zero, find if it is possible to split it in two subarrays (without reordering the elements), such that the sum of the two subarrays is the same. Print the two subarrays.

**Algorithm**

```
1. Calculate the total sum of the array.
2. Iterate through the array while maintaining a running sum (leftSum).
3. At each step, check if leftSum * 2 == total sum. If yes, a split is possible.
4. Split the array at that index and print both subarrays.
```

**Code**

```
public class SplitEqualSubarrays {

    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 3}; // Example array
        int total = 0;

        // Step 1: Compute total sum
        for (int num : arr) {
            total += num;
        }

        int leftSum = 0;

        // Step 2: Iterate and check if a split point exists
        for (int i = 0; i < arr.length - 1; i++) {
            leftSum += arr[i];
            int rightSum = total - leftSum;

            if (leftSum == rightSum) {
                // Step 3: Print subarrays
                System.out.print("Subarray 1: ");
                for (int j = 0; j <= i; j++) {
                    System.out.print(arr[j] + " ");
                }

                System.out.print("\nSubarray 2: ");
                for (int j = i + 1; j < arr.length; j++) {
                    System.out.print(arr[j] + " ");
                }

                return;
            }
        }

        System.out.println("No equal split possible.");
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
