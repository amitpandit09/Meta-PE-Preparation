
Write a function to sort a list of integers that looks like this [5,2,0,3,0,1,6,0] -> [1,2,3,5,6,0,0,0] in the most efficient way

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
