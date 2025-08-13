**Question**

You are given a string sentence that consist of words separated by spaces. Each word consists of lowercase and uppercase letters only.

We would like to convert the sentence to "Goat Latin" (a made-up language similar to Pig Latin.) The rules of Goat Latin are as follows:

If a word begins with a vowel ('a', 'e', 'i', 'o', or 'u'), append "ma" to the end of the word.
For example, the word "apple" becomes "applema".
If a word begins with a consonant (i.e., not a vowel), remove the first letter and append it to the end, then add "ma".
For example, the word "goat" becomes "oatgma".
Add one letter 'a' to the end of each word per its word index in the sentence, starting with 1.
For example, the first word gets "a" added to the end, the second word gets "aa" added to the end, and so on.
Return the final sentence representing the conversion from sentence to Goat Latin.

**Explanation**

1. Build a vowel set.(optional)
2. Split 'S' to words.
3. For each word, check if it starts with a vowel. (O(1) complexity with set).
4. If it does, keep going. If it does not, rotate the first letter to the end.
5. Add it to result string.

```
    class Solution {
    public String toGoatLatin(String S) {
        Set<Character> vowel = new HashSet<>();
        for(char c:"aeiouAEIOU".toCharArray()) vowel.add(c);
        StringBuilder res = new StringBuilder();
        int count=0;
        for(String curr:S.split(" ")) {
            count++;
            if(count>1) res.append(" ");
            if(vowel.contains(curr.charAt(0)))
                res.append(curr);
            else 
                res.append(curr.substring(1)+curr.charAt(0));
            res.append("ma");
            for(int j=0; j<count; j++)
                res.append("a");
        }
        return res.toString();
    }
}
```
