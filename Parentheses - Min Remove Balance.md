**Balance Parentheses**

 Given a string `str` consisting of parentheses `(`, `)` and alphanumeric characters. Remove minimum number of paranthesis to make the string valid and return **any** valid result. In a valid string for every opening/closing parentheses there is a matching closing/opening one.

**Clarifications**
1. Do we need to preserve the order of characters?
2. What characters are allowed in the string besides parentheses?
3. What’s the maximum length of the input string?
4. What should be returned if the string is already valid?
5. Are empty strings considered valid results?

**Solution approaches**
 1. Stack - O(n)
 2. Two-Pass Scan (Without Using a Stack)- Best Optimal Solution O(n)
 3. Breadth-First Search (BFS) – Return All Valid Results- Worst-case O(2^n)

**Example 1:**

```
Input: "ab(a(c)fg)9)"
Output: "ab(a(c)fg)9" or "ab(a(c)fg9)" or "ab(a(cfg)9)"
```

**Example 2:**

```
Input: ")a(b)c()("
Output: "a(b)c()"
```



**Example 3:**



```
Input: ")("
Output: ""
```



**Example 4:**



```
Input: "a(b))"
Output: "a(b)"
```



**Example 5:**



```
Input: "(a(c()b)"
Output: "a(c()b)" or "(ac()b)" or "(a(c)b)"
```



**Example 6:**



```
Input: "(a)b(c)d(e)f)(g)"
Output: "(a)b(c)d(e)f(g)"
```



## Code

```java
import java.util.*;


public class BalanceParantheses {
    public static void main(String[] args){
        String str = "(a(c()b)";
        System.out.println("Result" + minRemoveToMakeValid(str));
    }
        public static String minRemoveToMakeValid(String s) {
            // Pass 1: Remove all invalid ")"
            StringBuilder sb = new StringBuilder();
            int openSeen = 0;
            int balance = 0;
            for (int i = 0; i < s.length(); i++) {
                char c = s.charAt(i);
                if (c == '(') {
                    openSeen++;
                    balance++;
                } if (c == ')') {
                    if (balance == 0) continue;
                    balance--;
                }
                sb.append(c);
            }

            // Pass 2: Remove the rightmost "("
            StringBuilder result = new StringBuilder();
            int openToKeep = openSeen - balance;
            for (int i = 0; i < sb.length(); i++) {
                char c = sb.charAt(i);
                if (c == '(') {
                    openToKeep--;
                    if (openToKeep < 0) continue;
                }
                result.append(c);
            }

            return result.toString();
        }
}

```
