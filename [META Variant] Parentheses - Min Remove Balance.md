**Question** 

Given a string s that contains letters/digits and any of the brackets (), {}, [], delete the fewest brackets so that the string becomes balanced (every opening bracket has a matching closing bracket of the same type, in the correct order).
Return any valid resulting string. (Non-bracket characters must be kept in place.)

**Clarifying questions**

1. (, [, { ? Yes
2. Input? std::string? Yes
3. Output? std::string? Yes
4. 0–9, a–z lowercase? Yes
5. . (and then .)? Yes
6. Length of string? 1 <= N <= 1,000

**Code**

```
import java.util.*;

public class Solution {
    public String minRemoveToMakeValid(String s) {
        int n = s.length();
        // positions to delete
        boolean[] remove = new boolean[n];

        // stack holds pairs: (char, index)
        Deque<int[]> st = new ArrayDeque<>();

        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);

            if (isOpen(c)) {
                st.push(new int[]{c, i});
            } else if (isClose(c)) {
                if (!st.isEmpty() && match((char) st.peek()[0], c)) {
                    st.pop(); // good pair
                } else {
                    remove[i] = true; // mismatched/extra closing
                }
            }
            // letters/digits are ignored by logic (always kept)
        }

        // any leftover opens are unmatched -> remove them
        while (!st.isEmpty()) {
            remove[st.pop()[1]] = true;
        }

        // build answer skipping marked positions
        StringBuilder sb = new StringBuilder(n);
        for (int i = 0; i < n; i++) {
            if (!remove[i]) sb.append(s.charAt(i));
        }
        return sb.toString();
    }

    private boolean isOpen(char c) {
        return c == '(' || c == '{' || c == '[';
    }

    private boolean isClose(char c) {
        return c == ')' || c == '}' || c == ']';
    }

    private boolean match(char open, char close) {
        return (open == '(' && close == ')') ||
               (open == '{' && close == '}') ||
               (open == '[' && close == ']');
    }
}

```
