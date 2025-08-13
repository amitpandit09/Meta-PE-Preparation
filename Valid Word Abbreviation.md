A string can be abbreviated by replacing any number of non-adjacent, non-empty substrings with their lengths. The lengths should not have leading zeros.

For example, a string such as "substitution" could be abbreviated as (but not limited to):

"s10n" ("s ubstitutio n")
"sub4u4" ("sub stit u tion")
"12" ("substitution")
"su3i1u2on" ("su bst i t u ti on")
"substitution" (no substrings replaced)

```
class Solution {
    public boolean validWordAbbreviation(String word, String abbr) {
        int i=0;
        int j=0;

        while(j<abbr.length()){
            char ch = abbr.charAt(j);
            if(Character.isDigit(ch)){
                if(ch == '0'){
                    return false;
                }

                int num = 0;
                while(j<abbr.length() && Character.isDigit(abbr.charAt(j))){
                    num = num * 10 + (abbr.charAt(j)- '0');
                    j++;
                }
                i+=num;
            }else{
                if(i >= word.length() || word.charAt(i)!=ch){
                    return false;
                }
                i++;
                j++;
            }
        
        }
        return i == word.length();
    }
}
```
TC: O(abbr) | SC: O(1)
