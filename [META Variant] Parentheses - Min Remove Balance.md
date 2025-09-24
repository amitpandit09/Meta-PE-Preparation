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
        boolean[] remove = new boolean[n];

        // mapping closing → opening
        Map<Character, Character> mapping = new HashMap<>();
        mapping.put(')', '(');
        mapping.put(']', '[');
        mapping.put('}', '{');

        Deque<int[]> stack = new ArrayDeque<>();

        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);

            if (mapping.containsValue(c)) { // it's an opening bracket
                stack.push(new int[]{c, i});
            } else if (mapping.containsKey(c)) { // it's a closing bracket
                if (!stack.isEmpty() && stack.peek()[0] == mapping.get(c)) {
                    stack.pop();  // matched
                } else {
                    remove[i] = true; // unmatched closing
                }
            }
            // non-bracket chars always pass through
        }

        // leftover opens are unmatched
        while (!stack.isEmpty()) {
            remove[stack.pop()[1]] = true;
        }

        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < n; i++) {
            if (!remove[i]) sb.append(s.charAt(i));
        }
        return sb.toString();
    }
}


```
