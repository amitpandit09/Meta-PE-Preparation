Given the root of a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.

```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        if(root== null){
            return result;
        }
        
        Queue<TreeNode> queue = new LinkedList<TreeNode>();
        queue.add(root);
        TreeNode prev=null;

        while(!queue.isEmpty()){
            int size = queue.size();
            for(int i=size;i>0;i--){
                TreeNode node = queue.remove();
                if(node.left !=null){
                    queue.add(node.left);
                }

                if(node.right !=null){
                    queue.add(node.right);
                }
                prev=node;
            }
            result.add(prev.val);
        }
        return result;
    }
}
```
