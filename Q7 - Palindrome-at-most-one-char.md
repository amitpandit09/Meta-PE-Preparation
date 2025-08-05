Given a string s, return true if the s can be a palindrome after deleting at most one character from it.

**Ways to solve**
1. Two-Pointer Greedy Approach (Optimal)
2. Brute Force Deletion Check O(n²)

**Algorithm**

```
1. Use two pointers: one at the start (left), one at the end (right).
2. Compare characters:
If equal: move both pointers inward.
If not equal: try skipping either the left or right character and check if the remaining substring is a palindrome.
3. If either skip works, return true. If not, return false.
```

**Code**

```
public class ValidPalindrome {

    public static void main(String[] args) {
        String s = "abca";
        System.out.println(validPalindrome(s)); // true
    }

    public static boolean validPalindrome(String s) {
        int left = 0, right = s.length() - 1;

        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                // Try skipping either left or right character
                return isPalindrome(s, left + 1, right) || isPalindrome(s, left, right - 1);
            }
            left++;
            right--;
        }

        return true;
    }

    private static boolean isPalindrome(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left++) != s.charAt(right--)) {
                return false;
            }
        }
        return true;
    }
}

```
