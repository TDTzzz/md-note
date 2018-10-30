# 链表--ListNode

#### 测试用例

```php
class ListNode
{
    public $next;
    public $val;

    public function __construct($val)
    {
        $this->next = null;
        $this->val = $val;
    }
}

//测试数据
$head=new ListNode(1);
$head->next=new ListNode(2);
$head->next->next=new ListNode(3);
$head->next->next->next=new ListNode(4);
$head->next->next->next->next=new ListNode(5);
```



### 1.反转链表

```php
//旋转链表(非递归)
function reverse($head)
{
    if (empty($head)) return null;
    $newHead=null;
    $p=$head;

    while(!empty($head->val)){
        $head=$head->next;
        $p->next=$newHead;
        $newHead=$p;
        $p=$head;
    }

    return $newHead;
}

//反转链表(递归)
function reverse2($head)
{
    if (empty($head)||empty($head->next)){
        return $head;
    }
    $newHead=reverse2($head->next);
    $head->next->next=$head;
    $head->next=null;
    return $newHead;
}
```





### 2.旋转链表

给定一个链表，旋转链表，将链表每个节点向右移动 *k* 个位置，其中 *k* 是非负数。

**示例 1:**

```
输入: 1->2->3->4->5->NULL, k = 2
输出: 4->5->1->2->3->NULL
解释:
向右旋转 1 步: 5->1->2->3->4->NULL
向右旋转 2 步: 4->5->1->2->3->NULL
```



```php
//旋转链表
function rotateRight($head, $k)
{
    if (empty($head)||empty($head->next)) return $head;
    //先求链表总长度
    $phead = $head;
    $count = 1;

    while ($phead->next != null) {
        $count++;
        $phead = $phead->next;
    }
    $index = $k % $count;
    //移动index个位置
    for ($i = 0; $i < $index; $i++) {
        $prev = $head;
        $start = $prev->next;

        while ($start->next != null) {
            $prev = $prev->next;
            $start = $start->next;
        }
        $start->next = $head;
        $prev->next = null;
        $head = $start;
    }
    return $head;
}
```



### 3.合并两个排序的链表

```php
//合并两个排序的链表
function Merge($p1, $p2)
{
    if(empty($p1)) return $p2;
    if(empty($p2)) return $p1;

    if($p1->val>$p2->val){
        $p2->next=Merge($p1,$p2->next);
        return $p2;
    }else {
        $p1->next = Merge($p1->next, $p2);
        return $p1;
    }
}
```



### 4.分隔链表



给定一个链表和一个特定值 *x*，对链表进行分隔，使得所有小于 *x* 的节点都在大于或等于 *x* 的节点之前。

你应当保留两个分区中每个节点的初始相对位置。

**示例:**

```
输入: head = 1->4->3->2->5->2, x = 3
输出: 1->2->2->4->3->5
```

```php
//分隔链表
function partition($head,$x)
{
    if(empty($head)) return $head;

    $leftList=new ListNode(0);
    $rightList=new ListNode(0);
    $small=$leftList;
    $big=$rightList;

    while(!empty($head)){
        if($head->val<$x){
            $small->next=new ListNode($head->val);
            $small=$small->next;
        }else{
            $big->next=new ListNode($head->val);
            $big=$big->next;
        }

        $head=$head->next;
    }

    //合并链表
    if (!empty($leftList->next)){
        $tmp=$leftList->next;
        while(!empty($tmp->next)){
            $tmp=$tmp->next;
        }
        $tmp->next=$rightList->next;
        return $leftList->next;
    }else{
        return $rightList->next;
    }
}
```





### 5.删除链表中的重复节点

> 递归法

```php
function deleteDuplication($pHead){
    if(!$pHead) return null;
    if($pHead&&!$pHead->next) return $pHead;
    
    if($pHead->val==$pHead->next->val){
        $cur=$pHead->next->next;
        while($cur!=null&&$cur->val==$pHead->val){
            $cur=$cur->next;
        }
        return deleteDuplication($cur);
    }else{
        $cur=$pHead->next;
        $pHead->next=deleteDuplication($cur);
        return $pHead;
    }
}
```



> 非递归法

```php
function deleteDuplication($pHead){
    $new=new ListNode(0);
    $new->next=$pHead;
    $res=$new;
    
    while($new->next){
        $flag=false;//判断是否重复
        $tmp=$new->next;
        while($tmp->val==$tmp->next->val){
            $flag=true;
            $tmp=$tmp->next;
        }
        if($flag){
            $new->next=$tmp->next;
        }else{
            $new=$new->next;
        }
    }
    return $res->next;
}
```

