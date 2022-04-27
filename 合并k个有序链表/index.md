# 合并K个有序链表


### [23\. 合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

Difficulty: **困难**


给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

**示例 1：**

```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

**示例 2：**

```
输入：lists = []
输出：[]
```

**示例 3：**

```
输入：lists = [[]]
输出：[]
```

**提示：**

*   `k == lists.length`
*   `0 <= k <= 10^4`
*   `0 <= lists[i].length <= 500`
*   `-10^4 <= lists[i][j] <= 10^4`
*   `lists[i]` 按 **升序** 排列
*   `lists[i].length` 的总和不超过 `10^4`

#### 观题思路

- 最开始看见题目的时候想法就和合并两个有序链表一样，每次我将n个链表的最小的一个加入res链表中，注意这道题的头节点不确定，所以最好设置一个虚拟的头节点以及插入的tail位置指针。这样想了之后，就需要解决几个问题，第一个包含每个有序链表的当前最前面的节点的容器是什么？可以用题目给你数组，每次将某个链表的当前节点加入res后都更新数组中的值，这样也就有可能导致数组的某一项值为空。第二个就是如何判断哪一个节点是最小值？简单就用循环遍历数组就知道哪一个是最小值了。然后第三个问题就是怎么判断所有节点都已经加入了res了？数组的三个都为空的时候。至此，不考虑最优解的情况下，此题目按理说已经可以写出来了。(`代码参见代码一`)。
- 通过上述的解法虽然说是解出来了，但是发现300+ms的运行时间，然后去看了题解，发现很多其他的精彩思路。
- 首先先想一下上面我们是怎么去拿到每次最小的？是不是每次都需要比k个，有没有方法可以让比较的次数少一点呢？有！答案就是维护一个有序队列，将每个链表的当前节点都放入这个有序队列中，然后拿出队首最小的元素，再将队首最小的元素的下一个放入队列中，这样是不是就省去了每次都去比较。而有序队列在Java中有相关数据结构为PriorityQueue，直接用就好了。(`代码参考代码二`)
- 我们都知道怎么去合并两个有序链表，那么n个可不可以转化为合并两个有序数组呢？可以的，先将前两个数组合并，得到结果数组，再将结果数组和后面一个数组合并，依次迭代，则可以得到答案。为了编写方便可以考虑把第一个有序链表就看做就是前面的结果数组。(`代码参考代码三`)。
- 上述我们知道了逐个一个一个合并这样就可以拿到最终解决了，但是我们想一想，排序里面，我们除了可以慢慢的将一个数组排成有序，是不是还可以分治，两两间合并。(`代码参考代码四`)

#### Solution

Language: **Java**

##### 代码一：

```Java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode head = new ListNode();
        ListNode point = head;
        boolean flag = true;
        while (flag) {
            flag = false;
            int tempMin = Integer.MAX_VALUE;
            int index = 0;
            for (int i = 0;i < lists.length;i++) {
                if (lists[i] != null && lists[i].val < tempMin) {
                    tempMin = lists[i].val;
                    index = i;
                    flag = true;
                }
            }
            if (flag) {
                point.next = lists[index];
                point = point.next;
                lists[index] = lists[index].next;
            }
        }
        return head.next;q
    }
}
```

##### 代码二：

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode ans = null;
        for (int i = 0; i < lists.length; ++i) {
            ans = mergeTwoLists(ans, lists[i]);
        }
        return ans;
    }

    public ListNode mergeTwoLists(ListNode a, ListNode b) {
        if (a == null || b == null) {
            return a != null ? a : b;
        }
        ListNode head = new ListNode(0);
        ListNode tail = head, aPtr = a, bPtr = b;
        while (aPtr != null && bPtr != null) {
            if (aPtr.val < bPtr.val) {
                tail.next = aPtr;
                aPtr = aPtr.next;
            } else {
                tail.next = bPtr;
                bPtr = bPtr.next;
            }
            tail = tail.next;
        }
        tail.next = (aPtr != null ? aPtr : bPtr);
        return head.next;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/he-bing-kge-pai-xu-lian-biao-by-leetcode-solutio-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##### 代码三：

```Java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        return merge(lists, 0, lists.length - 1);
    }

    public ListNode merge(ListNode[] lists, int l, int r) {
        if (l == r) {
            return lists[l];
        }
        if (l > r) {
            return null;
        }
        int mid = (l + r) >> 1;
        return mergeTwoLists(merge(lists, l, mid), merge(lists, mid + 1, r));
    }

    public ListNode mergeTwoLists(ListNode a, ListNode b) {
        if (a == null || b == null) {
            return a != null ? a : b;
        }
        ListNode head = new ListNode(0);
        ListNode tail = head, aPtr = a, bPtr = b;
        while (aPtr != null && bPtr != null) {
            if (aPtr.val < bPtr.val) {
                tail.next = aPtr;
                aPtr = aPtr.next;
            } else {
                tail.next = bPtr;
                bPtr = bPtr.next;
            }
            tail = tail.next;
        }
        tail.next = (aPtr != null ? aPtr : bPtr);
        return head.next;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/he-bing-kge-pai-xu-lian-biao-by-leetcode-solutio-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##### 代码四：

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        PriorityQueue<ListNode> q = new PriorityQueue<>((x,y)->x.val-y.val);
        for(ListNode node : lists){
            if(node!=null){
                q.add(node);
            }
        }
        ListNode head = new ListNode(0);
        ListNode tail = head;
        while(!q.isEmpty()){
            tail.next = q.poll();
            tail = tail.next;
            if (tail.next != null){
                q.add(tail.next);
            }
        }
        return head.next;
    }
}
```


