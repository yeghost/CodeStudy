# 链表

## Easy

### [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

> 将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

#### 递归

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if(l1==nullptr)
        {
            return l2;
        }
        else if(l2==nullptr)
        {
            return l1;
        }
        else if(l1->val < l2-> val)
        {
            l1->next = mergeTwoLists(l1->next,l2);
            return l1;
        }
        else
        {
            l2->next = mergeTwoLists(l1,l2->next);
            return l2;
        }
        
    }
};
```

#### 迭代

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode * preHead = new ListNode(-1);
        ListNode*prev =preHead;
        while(l1!=nullptr&&l2!=nullptr)
        {
            if(l1->val < l2->val)
            {
                prev->next=l1;
                l1=l1->next;
            }
            else
            {
                prev->next=l2;
                l2=l2->next;               
            }
            prev=prev->next;
        }
        prev->next= l1 == nullptr?l2:l1;
        return preHead->next;
    }
};
```

### [83. 删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

#### 直接法

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode* current = head;
        while(current!=NULL && current->next!=NULL)
        {
            if(current->val==current->next->val)
            {
                current->next=current->next->next;
            }else
            {
                current=current->next;
            }
        }
        return head;
    }
};
```

### [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

#### 哈希表

遍历所有节点，看之前是否访问过，比较容易想到的办法

#### 快慢指针

比较精妙的方法，假如有环，快指针必然能从后面追上去。小细节，快慢指针刚开始不在同一个位置，是为了进入while循环，改为do-while可以放在同一个位置。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(head==nullptr||head->next==nullptr)
        {
            return false;
        }
        ListNode* slow=head;
        ListNode* fast=head->next;
        while(slow!=fast)
        {
            if(fast==nullptr||fast->next==nullptr)
            {
                return false;
            }
            slow=slow->next;
            fast=fast->next->next;
        }
        return true;
    }
};
```

### [160. 相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

#### 暴力法

对链表A中的每一个结点 a_i，遍历整个链表 B并检查链表B中是否存在结点和 a_i相同。

- 时间复杂度 : (mn)。
- 空间复杂度 : *O*(1)。

#### 哈希表法

暴力法中每一次都需要遍历整个链表B来查询是否存在相同节点，为了减小时间复杂度，我们可以提前用map来存储链表B中的元素，查询是否存在时就不需要遍历链表B。

- 时间复杂度 : *O*(*m*+*n*)。
- 空间复杂度 : *O*(*m*) 或 *O(n)*。

#### 双指针法

这是本题最精妙的解法，下面是力扣的解法

* 创建两个指针 pA和 pB，分别初始化为链表 A 和 B 的头结点。然后让它们向后逐结点遍历。
* 当 pA到达链表的尾部时，将它重定位到链表 B 的头结点 (你没看错，就是链表 B); 类似的，当 pBpB 到达链表的尾部时，将它重定位到链表 A 的头结点。
* 若在某一时刻 pA和 pB相遇，则 pA/pB为相交结点。

实际上是这个意思，有两个指针，假如他们速度一样，最后相遇，说明他们两个走过的距离应该相同。但是两个链表不一定一样长，就很难保证同时走到相交处。但是两个链表的和是固定的，假如每个指针走完本条链再走另外一条链，相当于他们走的长度为A+B的和减去最后公共的部分。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if(headA==nullptr||headB==nullptr)
        {
            return nullptr;
        }
        ListNode* pa=headA;
        ListNode* pb=headB;
        while(pa != pb)
        {
            if(pa==nullptr)
            {
                pa=headB;
            }
            else
            {
                pa=pa->next;
            }
            if(pb==nullptr)
            {
                pb=headA;
            }
            else
            {
                pb=pb->next;
            }
        }
        return pa;
    }
};
```



## Medium



## hard

# 每日一题

