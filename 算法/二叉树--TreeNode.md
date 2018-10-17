# 二叉树--TreeNode

### 1.中序遍历二叉树

> 简单的递归版

```
function dfs(){
}
```



> 有难度且效率高的迭代法

```



```



### 2.二叉树的镜像

```php
function Mirror(&$root)
{
    if(empty($root)) return;
    
    $tmp=$root->left;
    $root->left=$root->right;
    $root->right=$tmp;
    
    Mirror($root->left);
    Mirror($root->right);
}
```





### 3.二叉树的深度

```php
function TreeDepth($root)
{
    return $root==null?0:max(TreeDepth($root->left),TreeDepth($root->right))+1;
}
```





### 4.平衡二叉树

> 给定一棵二叉树，判断是否是平衡二叉树

```php
function IsBalanced_Solution($root)
{
    return getDepth($root) != -1;
}

function getDepth($root)
{
    if (empty($root))
        return 0;

    $left = getDepth($root->left);

    if ($left == -1) {
        return -1;
    }

    $right = getDepth($root->right);

    if ($right == -1) {
        return -1;
    }

    return abs($left - $right) > 1 ? -1 : 1 + max($left, $right);
}
 
```





### 5.对称的二叉树

> 请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

```php
function isSymmetrical($pRoot)
{
    // write code here
    if(empty($pRoot)) return true;
      
    return help($pRoot->left,$pRoot->right);
}

function help($left,$right){
    if(empty($left)&&empty($right)){
        return true;
    }    
     
    if(empty($left)||empty($right)){
        return false;
    }
    
    if($left->val!=$right->val){
        return false;
    }
    
    return help($left->left,$right->right)&&help($left->right,$right->left);        
}
```





### 6.填充同一层的兄弟节点

> 给定一个二叉树
>
>
>
> ```
> struct TreeLinkNode {
>   TreeLinkNode *left;
>   TreeLinkNode *right;
>   TreeLinkNode *next;
> }
> ```
>
> 填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 `NULL`。
>
> 初始状态下，所有 next 指针都被设置为 `NULL`。
>
> **示例:**
>
> 给定完美二叉树，
>
> ```
>      1
>    /  \
>   2    3
>  / \  / \
> 4  5  6  7
> ```
>
> 调用你的函数后，该完美二叉树变为：
>
> ```
>      1 -> NULL
>    /  \
>   2 -> 3 -> NULL
>  / \  / \
> 4->5->6->7 -> NULL
> ```



**简单易懂的递归法**

```javascript
    var connect = function (root) {
        if(!root) return;

        if(root.left) root.left.next=root.right;
        if(root.right) root.right.next=root.next?root.next.left:null;

        connect(root.left);
        connect(root.right);
    };
```



**更优秀的迭代法**

```javascript
    var connect=function(root)
    {
        if(!root) return;
        var cur=null;
        var start=root;

        while(start.left){
            cur=start;
            while(cur){
                cur.left.next=cur.right;
                if(cur.next) cur.right.next=cur.next.left;
                cur=cur.next;
            }

            start=start.left;
        }
    }
```



### 7.填充同一层的兄弟节点II

> 与之前的题目比，少了完美二叉树的条件
>
> **示例:**
>
> 给定二叉树，
>
> ```
>      1
>    /  \
>   2    3
>  / \    \
> 4   5    7
> ```
>
> 调用你的函数后，该二叉树变为：
>
> ```
>      1 -> NULL
>    /  \
>   2 -> 3 -> NULL
>  / \    \
> 4-> 5 -> 7 -> NULL
> ```



```javascript
    var connect = function (root) {
        var dummpy=new TreeLinkNode(0);
        var t=dummpy;

        while(root){
            if(root.left){
                t.next=root.left;
                t=t.next;
            }

            if(root.right){
                t.next=root.right;
                t=t.next;
            }

            root=root.next;
            if(!root){
                t=dummpy;
                root=dummpy.next;
                dummpy.next=null;
            }
        }
    }
```



### 8.求根到叶子结点数字之和

> 给定一个二叉树，它的每个结点都存放一个 `0-9` 的数字，每条从根到叶子节点的路径都代表一个数字。
>
> 例如，从根到叶子节点路径 `1->2->3` 代表数字 `123`。
>
> 计算从根到叶子节点生成的所有数字之和。
>
> **说明:** 叶子节点是指没有子节点的节点。
>
> **示例 1:**
>
> ```
> 输入: [1,2,3]
>     1
>    / \
>   2   3
> 输出: 25
> 解释:
> 从根到叶子节点路径 1->2 代表数字 12.
> 从根到叶子节点路径 1->3 代表数字 13.
> 因此，数字总和 = 12 + 13 = 25.
> ```



```javascript

    var sumNumbers = function (root) {
        if(!root) return 0;
        var tmp=0;
        var res=[0];
        help(root,tmp,res);
        return res[0];
    };

    var help=function(root,tmp,res){
        if(!root.left&&!root.right){
            res[0]+=tmp*10+root.val;
            return;
        }
        if(root.left) help(root.left,tmp*10+root.val,res);
        if(root.right) help(root.right,tmp*10+root.val,res);
    }


```

> 注意上面的JS代码有个地方有点怪异，res我用的是数组，因为js里的函数传参是值传递，如果用res=0传的话，res始终是0，而用数组的话，因为数组作函数参数，传的是内存地址，所以可以在函数内部修改。故用了数组。但其实应该不需要res这个参数，直接给个全局变量res即可，但不知道为何在leetcode里没通过？？

