
**Question**

Write a function to sort a list of integers that looks like this [5,2,0,3,0,1,6,0] -> [1,2,3,5,6,0,0,0] in the most efficient way

**Possible Solutions**

1. Separate, Sort, and Rebuild
2. In-place Sorting with Index Swaps
3. In-place Partial Sort (Advanced)
4. Stable Two-Pass Approach (Preserve relative non-zero order) 

**Solution**

Solution for : 1. Separate, Sort, and Rebuild

Python

```
def sort_with_zeros_at_end(nums):
    non_zeros = sorted([x for x in nums if x != 0])
    return non_zeros + [0] * nums.count(0)

```

Java

```
import java.util.*;

public class SortNonZeroThenZero {
    public static List<Integer> sortList(List<Integer> nums) {
        List<Integer> nonZeros = new ArrayList<>();
        int zeroCount = 0;

        // Separate non-zero numbers and count zeros
        for (int num : nums) {
            if (num == 0) {
                zeroCount++;
            } else {
                nonZeros.add(num);
            }
        }

        // Sort the non-zero part
        Collections.sort(nonZeros);

        // Append zeros at the end
        for (int i = 0; i < zeroCount; i++) {
            nonZeros.add(0);
        }

        return nonZeros;
    }

    public static void main(String[] args) {
        List<Integer> input = Arrays.asList(5, 2, 0, 3, 0, 1, 6, 0);
        List<Integer> result = sortList(input);
        System.out.println(result); // Output: [1, 2, 3, 5, 6, 0, 0, 0]
    }
}

```


**Complexity**

Time Complexity:

O(n) for separating elements
O(m log m) for sorting non-zero elements
O(n) for final list building
➤ Overall: O(n + m log m), where m = non-zero elements.

Space Complexity: O(n) for new list.

-------

Solution for : 5. Counting Sort Variant (if values are small integers)
```
def counting_sort_with_zeros(nums):
    from collections import Counter
    counter = Counter(x for x in nums if x != 0)
    result = []
    for i in range(1, max(counter.keys()) + 1):
        result.extend([i] * counter[i])
    result.extend([0] * nums.count(0))
    return result


```
Complexity
Time: O(n + k), where k = range of values

Space: O(k)


