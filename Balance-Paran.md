# # Facebook | Balance Parentheses

 Given a string `str` consisting of parentheses `(`, `)` and alphanumeric characters. Remove minimum number of paranthesis to make the string valid and return **any** valid result. In a valid string for every opening/closing parentheses there is a matching closing/opening one. 

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
class Solution {
    public String minRemoveToMakeValid(String s) {
        StringBuilder sb = new StringBuilder(s);
        Stack<Integer> stack = new Stack<>();
        for(int i=0;i<sb.length();i++) {
            if(sb.charAt(i) == '('){
                stack.add(i);
            }

            if(sb.charAt(i) == ')') {
                if(!stack.empty()){
                    stack.pop();
                }
                else{
                    sb.setCharAt(i, '*');
                }
            }
        }

        while(!stack.empty()){
            sb.setCharAt(stack.pop(), '*');
        }
        return sb.toString().replaceAll("\\*", "");
    }
}
```
