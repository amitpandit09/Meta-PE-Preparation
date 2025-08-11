# Facebook | Phone Screen | Redundant Paths

**Question 1:**
Given a list of directory info, return all paths which are not redundant. A redundant path is one with the root already on the list.



Ex: Input: ["/a/b/c","/a/b","/d/e","/d"]
Output: ["/a/b" , "/d"]



Similar to: https://leetcode.com/problems/find-duplicate-file-in-system/



You want to remove any path that’s a subdirectory of another path already present. Two good ways:

# 1) Sort + single pass (simple and fast)

Idea: lexicographically sort the paths so parents come before their children. Keep a path only if the last kept root is **not** its prefix (with a `/` boundary).

### Python

```python
def non_redundant_paths(paths):
    paths = sorted(paths)
    res = []

    def is_subpath(p, root):
        # p is redundant if it equals root or starts with root + '/'
        return p == root or p.startswith(root + "/")

    for p in paths:
        if not res or not is_subpath(p, res[-1]):
            res.append(p)
    return res

# Example
print(non_redundant_paths(["/a/b/c","/a/b","/d/e","/d"]))  # ['/a/b', '/d']
```

### Java

```java
import java.util.*;

public class Main {
    public static List<String> nonRedundant(List<String> paths) {
        Collections.sort(paths);
        List<String> res = new ArrayList<>();

        for (String p : paths) {
            if (res.isEmpty()) {
                res.add(p);
            } else {
                String root = res.get(res.size() - 1);
                if (!(p.equals(root) || p.startsWith(root + "/"))) {
                    res.add(p);
                }
            }
        }
        return res;
    }

    public static void main(String[] args) {
        System.out.println(nonRedundant(new ArrayList<>(Arrays.asList(
            "/a/b/c","/a/b","/d/e","/d"
        )))); // [/a/b, /d]
    }
}
```

**Complexity:** Sorting dominates → **O(n log n + L)** time (L = total length of strings), **O(1)** extra (besides output).

---

# 2) Hash-set prefix test (no sort; linear in total length)

Idea: put all paths in a set. A path is redundant if **any** of its proper prefixes (ending at a `/`) is in the set.

### Python

```python
def non_redundant_paths_set(paths):
    s = set(paths)
    res = []
    seen = set()  # to dedupe roots in output
    for p in paths:
        redundant = False
        idx = 1
        while True:
            idx = p.find('/', idx)
            if idx == -1: break
            if p[:idx] in s:  # some parent exists
                redundant = True
                break
            idx += 1
        if !redundant and p not in seen:
            res.append(p)
            seen.add(p)
    return res

print(non_redundant_paths_set(["/a/b/c","/a/b","/d/e","/d"]))  # ['/a/b', '/d']
```

**Complexity:** For each path, you check its `/`-split prefixes; summed over all paths this is **O(L)** expected time, **O(n)** space for the set.

---

## Which to use?

* Want the **simplest** code? ➜ **Sort + single pass**.
* Want to avoid sorting and stay near **linear** in total characters? ➜ **Hash-set prefix test**.

Both return roots only: `["/a/b", "/d"]` for your example.
