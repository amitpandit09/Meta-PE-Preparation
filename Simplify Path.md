You are given an absolute path for a Unix-style file system, which always begins with a slash '/'. Your task is to transform this absolute path into its simplified canonical path.

The rules of a Unix-style file system are as follows:

A single period '.' represents the current directory.
A double period '..' represents the previous/parent directory.
Multiple consecutive slashes such as '//' and '///' are treated as a single slash '/'.
Any sequence of periods that does not match the rules above should be treated as a valid directory or file name. For example, '...' and '....' are valid directory or file names.


```
class Solution {
    public String simplifyPath(String path) {
        Stack<String> stack = new Stack<>();
        String[] components = path.split("/");

        for(String component : components) {
            if(component.equals("") || component.equals(".")) {
                continue;
            }
            else if(component.equals("..")){
                if(!stack.isEmpty()){
                    stack.pop();
                }
            }
            else{
                stack.push(component);
            }
        }

StringBuilder simplePath = new StringBuilder();
for(String dir : stack){
    simplePath.append("/").append(dir);
}

return simplePath.length() > 0 ? simplePath.toString() : "/";
        
    }
}

```
