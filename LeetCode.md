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

## [237. 删除链表中的节点](https://leetcode-cn.com/problems/delete-node-in-a-linked-list/)

删除链表中的节点，因为题目中保证不是最后一个节点，所以直接将下一个节点的值赋给当前节点，然后删除下一个节点

```java
class Solution {
    public void deleteNode(ListNode node) {
        node.val=node.next.val;
        node.next=node.next.next;
    }
}
```

## [397. 整数替换](https://leetcode-cn.com/problems/integer-replacement/)

本题之所以存在最小替换，是因为n为奇数时，选择+1和-1对最终结果影响不一样

### 递归

有个优化细节，奇数，可以一下子判断两步，从而将递归简化为n/2,否则会溢出。并且注意2<sup>32</sup>+1会溢出

~~~java
class Solution {
    public int integerReplacement(int n) {
        if(n==1)
        {
            return 0;
        }
        else if(n%2==0)
        {
            return 1+integerReplacement(n/2);
        }else
        {
            return 2 + Math.min(integerReplacement(n/2),integerReplacement(n/2+1));
        }
    }
}
~~~

### 记忆化

递归里面有些时间浪费，因为有些n的值被重复计算，可以用map记录下来

~~~java
class Solution {
    Map<Integer, Integer> tmp = new HashMap<Integer, Integer>();

    public int integerReplacement(int n) {
        if(n==1)
        {
            return 0;
        }
        if(!tmp.containsKey(n))
        {
            if(n%2==0)
            {
                tmp.put(n,1+integerReplacement(n/2));
            }
            else
            {
                tmp.put(n,2+Math.min(integerReplacement(n/2),integerReplacement(n/2+1)));
            }
        }
        return tmp.get(n);
    }
}

~~~

### 贪心

应该是本题的最优解，当n为偶数时，n/2是唯一操作，当为奇数时，如何判断+1还是-1是关键

n为奇数n%4为1或3

1. n%4 == 1时-1为最优解

   如果n-1，结果(n-1)/2,为偶数，可以变化为(n-1)/4，2步，变为(n+3)/4，3步

   如果n+1，结果(n+1)/2,为奇数，+1，变为(n+3)/4,3步，-1变为(n-1)/4,3步

   可以看出变成相同的结果 -1方法优于+1

2. n%4==3时，+1为最优解

   如果n+1，结果(n+1)/2,为偶数，可以变化为(n+1)/4，2步，变为(n-1)/4，3步

   如果n-1，结果(n-1)/2,为奇数，+1，变为(n+3)/4,2步，-1变为(n-3)/4,3步

   可以看出变成相同的结果 -1方法优于+1

   3要特殊考虑，因为不需要后续操作

~~~java
class Solution {
    public int integerReplacement(int n) {
        int ans=0;
        if(n==1)
        {
            return 0;
        }
        while(n > 1)
        {
            if(n%2==0)
            {
                n=n/2;
                ans++;
            }
            else
            {
                if(n == 3)
                {
                    ans+=2;
                    return ans;
                }
                else if(n%4 == 1)
                {
                    n=n/2;
                    ans+=2;
                }
                else
                {
                    n=n/2+1;
                    ans+=2;
                }
            }
        }
        return ans;
    }
}
~~~



## [412. Fizz Buzz](https://leetcode-cn.com/problems/fizz-buzz/)

题目比较简单，直接遍历，然后判断是否为3，5的倍数，时间复杂度为O(n)

~~~c++
class Solution {
public:
    vector<string> fizzBuzz(int n) {
        vector<string> ans(n);
        for(int i=0;i<n;i++)
        {
            int t=i+1;
            if(t%15==0)
            {
                ans[i]="FizzBuzz";
            }else if(t%5==0)
            {
                ans[i]="Buzz";
            }
            else if(t%3==0)
            {
                ans[i]="Fizz";
            }
            else 
            {
                ans[i]=to_string(t);
            }
        }
        return ans;
    }
};
~~~

## [492. 构造矩形 ](https://leetcode-cn.com/problems/construct-the-rectangle/)

先开平方，然后依次判断是否可以整除即可

```c++
class Solution {
public:
    vector<int> constructRectangle(int area) {
        int w = sqrt(1.0 * area);
        while(area%w)
        {
            --w;
        }
        return {area/w,w};
    }
};
```

## [563. 二叉树的坡度 ](https://leetcode-cn.com/problems/binary-tree-tilt/)

### dfs

```java
class Solution {
    int ans=0;
    public int findTilt(TreeNode root) {
        dfs(root);
        return ans;
    }
    public int dfs(TreeNode root)
    {
        if(root==null)
        {
            return 0;
        }
        int left = dfs(root.left);
        int right = dfs(root.right);
        ans+=Math.abs(right-left);
        return left+right+root.val;
    }
}
```

## [1446. 连续字符 ](https://leetcode-cn.com/problems/consecutive-characters/)

遍历一遍即可

~~~java
class Solution {
    public int maxPower(String s) {
        int len = s.length();
        if(len <= 0)
        {
            return 0;
        }
        int max=1,tmp=1;
        for(int i=1;i<len;i++)
        {
            if(s.charAt(i-1) == s.charAt(i))
            {
                tmp++;
                if(tmp > max)
                {
                    max = tmp;
                }
            }
            else
            {
                tmp = 1;
            }
        }
        return max;
    }
}
~~~



# 剑指offer

## [剑指 Offer 03. 数组中重复的数字 ](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/submissions/)

### 排序

通过对数组排序，相同的数字必定相邻，扫描一下就行，但是时间复杂度为O(nlogn)

~~~c++
class Solution {
public:
    int findRepeatNumber(vector<int>& nums) {
        sort(nums.begin(),nums.end());
        for(int i=0;i<nums.size()-1;i++)
        {
            if(nums[i]==nums[i+1])
            {
                return nums[i];
            }
        }
        return 0;
    }
};
~~~

### 哈希表

时间复杂度为O(n),空间复杂度为O(n)

```C++
class Solution {
public:
    int findRepeatNumber(vector<int>& nums) {
        unordered_map<int, bool> tmp;
        for(int i=0; i<nums.size(); i++)
        {
            if(tmp.count(nums[i]))
            {
                return nums[i];
            }
            tmp[nums[i]]=true;
        }
        return 0;
    }
};
```

### 遍历

这个遍历的方法比较取巧，因为n个数字对应0到n-1，那么只要在遍历的时候，把相应的值放在对应的位置，那么等下次再有值要放在对应位置的时候，就找到了重复数字。

```c++
class Solution {
public:
    int findRepeatNumber(vector<int>& nums) {
        for(int i=0; i<nums.size(); i++)
        {
            while(nums[i] != i)
            {
                if(nums[i] == nums[nums[i]])
                {
                    return nums[i];
                }
                int tmp = nums[i];
                nums[i] = nums[tmp];
                nums[tmp] = tmp;
            }
        }
        return 0;
    }
};
```

## [剑指 Offer 04. 二维数组中的查找 ](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

### 暴力搜索

遍历整个二维数组，时间复杂度O(mn),空间复杂度O(1)

### 线性查找

从右上角出发，如果右上角的数字等于target，已找到，如果大于target，向左移一列。小于target，这一行必定都小于target，向下移动一行。最多移动m列和n行。时间复杂度O(m+n),空间复杂度O（1）。

```c++
class Solution {
public:
    bool findNumberIn2DArray(vector<vector<int>>& matrix, int target) {
        int n=matrix.size();
        if(n == 0)
        {
            return false;
        }
        int m=matrix[0].size();
        int row=0,cloumn=m-1;
        while(row < n && cloumn >= 0)
        {
            if(matrix[row][cloumn] == target)
            {
                return true;
            }
            else if(matrix[row][cloumn] > target)
            {
                cloumn--;
            }
            else
            {
                row++;
            }
        }
        return false;
    }
};
```

需要一个判断数组是否为空的操作

## [剑指 Offer 05. 替换空格 ](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)

### 暴力

从前往后扫描，遇到一个空格就把数组后面的字符向后移动，时间复杂O（n<sup>2</sup>）,代价主要在后面字符需要多次移动

### 从后向前遍历

先遍历一次统计空格的个数，计算出最后字符的长度，从后往前遍历，这样之后移动后面的字符，字符不会被重复移动。

```c++
class Solution {
public:
    string replaceSpace(string s) {
        int count=0, length = s.size();
        int i=0;
        for(i=0;i<length;i++)
        {
            if(s[i]==' ')
            {
                count++;
            }
        }
        int newlength= count*2+length;
        s.resize(newlength);
        int right=length-1,left=newlength-1;
        while(right < left && right >= 0)
        {
            if(s[right]==' ')
            {
                s[left--]='0';
                s[left--]='2';
                s[left--]='%';
            }
            else
            {
                s[left--]=s[right];
            }
            right--;
        }
        return s;
    }
};
```

c++里面 string有个resize函数，可以改变数组大小。

这种题目要注意是在原字符串上进行修改，还是在新的字符串上修改。

原字符串修改的话，从前往后需要考虑多次的重复移动，而新的字符串则不需要考虑，因为后面是空的，不用担被覆盖的问题。

## [剑指 Offer 06. 从尾到头打印链表 ](https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

很明显后进先出，利用栈的结构辅助。

```c++
class Solution {
public:
    vector<int> reversePrint(ListNode* head) {
        stack<int> tmp; 
        vector<int> ans;
        ListNode* pre = head;
        while(pre != NULL)
        {
            tmp.push(pre->val);
            pre = pre->next;
        }
        while(!tmp.empty())
        {
            ans.push_back(tmp.top());
            tmp.pop();
        }
        return ans;
    }
};
```



## [剑指 Offer 10- I. 斐波那契数列 ](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

### 递归

时间复杂度指数级，很多值重复计算

### 动态规划

每一项只计算一次，时间复杂度O（n）

```c++
class Solution {
public:
    int fib(int n) {
        int MOD=1000000007;
        if(n<2)
        {
            return n;
        }
        int a=0,b=1,tmp=0;
        for(int i=2;i<=n;i++)
        {
            tmp=b;
            b=(a+b)%1000000007;
            a=tmp;
        }
        return b;
    }
};
```

### 矩阵快速幂

斐波那契数列数列，可以认为是

![image-20211028095059145](C:\Users\nty\AppData\Roaming\Typora\typora-user-images\image-20211028095059145.png)

![image-20211028095110726](C:\Users\nty\AppData\Roaming\Typora\typora-user-images\image-20211028095110726.png)

转化为一个矩阵的n次方和一个矩阵的乘积，优化的地方在于求n次方，我们可以使用快速幂，当然矩阵的快速幂比实数的快速幂复杂一点。

```c++
class Solution {
public:
    const int MOD = 1000000007;
    int fib(int n) {
       if(n<2)
       {
           return n;
       }
       vector<vector<long>> q = {{1,1},{1,0}};
       vector<vector<long>> ans = pow(q,n-1);
       return ans[0][0];
    }

    vector<vector<long>> pow(vector<vector<long>>& a,int n)
    {
        vector<vector<long>> ret ={{1,0},{0,1}};
        while(n > 0)
        {
            if(n & 1)
            {
                ret = multiply(ret,a);
            }
            n >>= 1;
            a = multiply(a,a);
        }
        return ret;
    }

    vector<vector<long>> multiply(vector<vector<long>>& a ,vector<vector<long>>& b)
    {
        vector<vector<long>> c = {{0,0},{0,0}};
        for(int i=0;i<2;i++)
        {
            for(int j=0;j<2;j++)
            {
                c[i][j]=(a[i][0]*b[0][j]+a[i][1]*b[1][j])%MOD;
            }
        }
        return c;
    }
};
```

## [剑指 Offer 11. 旋转数组的最小数字](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

### 遍历

```java
class Solution {
    public int minArray(int[] numbers) {
        int max=numbers[0];
        for(int data : numbers)
        {
            if(data < max)
            {
                max = data;
            }
        }
        return max;
    }
}
```

## [剑指 Offer 15. 二进制中1的个数](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)

### 循环检查二进制位

之所以左移是因为，右移可能会陷入死循环，时间复杂度O(k)

```java
public class Solution {
    public int hammingWeight(int n) {
        int ret = 0;
        for (int i = 0; i < 32; i++) {
            if ((n & (1 << i)) != 0) {
                ret++;
            }
        }
        return ret;
    }
}
```

### 位运算优化

把一个数减去1，那么二进制中最后以为1会变成0，求&，然后重复这个操作，能进行多少次就是有多少个1

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int count=0;
        while(n!=0)
        {
            n= n&(n-1);
            count++;
        }   
        return count;
    }
}
```

## [剑指 Offer 18. 删除链表的节点](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)

增加头节点遍历,当然这道题跟原题不太一样

```java
class Solution {
    public ListNode deleteNode(ListNode head, int val) {
        ListNode pre = new ListNode(0);
        pre.next=head;
        head = pre;
        while(pre.next != null)
        {
            if(pre.next.val==val)
            {
                pre.next=pre.next.next;
                break;
            }
            pre=pre.next;
        }
        return head.next;
    }
}
```

## [剑指 Offer 21. 调整数组顺序使奇数位于偶数前面](https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)

### 暴力

扫描，遇见偶数，拿出来，把后面的数字往前移一位，刚拿出来的偶数放在最后

O(n<sup>2</sup>)

### 头尾指针

left遇见奇数跳过，遇见偶数停下

right遇见偶数跳过，遇见奇数停下

互相交换，继续下一次循环

```java
class Solution {
    public int[] exchange(int[] nums) {
        int left = 0,right = nums.length-1,tmp=0;
        while(left<right)
        {
            while(left<right && nums[left]%2!=0)
            {
                left++;
            }
            while(left<right && nums[right]%2==0)
            {
                right--;
            }
            tmp=nums[left];
            nums[left]=nums[right];
            nums[right]=tmp;
        }
        return nums;
    }
}
```

## [剑指 Offer 22. 链表中倒数第k个节点](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

### 遍历

```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        int n = 0;
        ListNode node = null;

        for (node = head; node != null; node = node.next) {
            n++;
        }
        for (node = head; n > k; n--) {
            node = node.next;
        }

        return node;
    }
}
```

### 快慢指针

```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode fast = head;
        ListNode slow = head;

        while (fast != null && k > 0) {
            fast = fast.next;
            k--;
        }
        while (fast != null) {
            fast = fast.next;
            slow = slow.next;
        }

        return slow;
    }
}
```

## [剑指 Offer 24. 反转链表](https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/)

### 迭代

把当前节点的next设置为上一个节点，需要先存下来下一个节点，并且需要有个节点保存上一个节点

时间复杂度O(n),空间复杂度O(1)

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
}
```

### 递归

设置递归结束条件，head为空或head的下一个节点为空，将当前节点的下一个节点之后倒转得到新的头节点，将当前节点的下一个节点的指针指向当前节点。

当前节点的下一个节点置为空

时间复杂度O（n），空间复杂度O（n）

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head==null || head.next==null)
        {
            return head;
        }
        ListNode newHead = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return newHead;
    }
}
```

## [剑指 Offer 25. 合并两个排序的链表](https://leetcode-cn.com/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/)

### 遍历

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(0),curr= head;
        while(l1 != null && l2 != null)
        {
            if(l1.val < l2.val)
            {
                curr.next = l1;
                l1 = l1.next; 
            }
            else
            {
                curr.next = l2;
                l2 = l2.next;
            }
            curr = curr.next;
        }
        if(l1!=null)
        {
            curr.next = l1;
        }
        else 
        {
            curr.next = l2;
        }
        return head.next;
    }
}
```

## [剑指 Offer 53 - I. 在排序数组中查找数字 I](https://leetcode-cn.com/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)

### 遍历

从前到后遍历，找到一个+1

时间复杂度O(n),空间复杂度O(1)

```java
class Solution {
    public int search(int[] nums, int target) {
        if(nums.length == 0)
        {
            return 0;
        }
        int ans = 0;
        for(int e : nums)
        {
            if(e == target)
            {
                ans++;
            }
        }
        return ans;
    }
}
```

### 二分

找到第一个出现target的下标和最后一个target的下标，两者相减即可

时间复杂度O(logn),空间复杂度O(1)

```java
class Solution {
    public int search(int[] nums, int target) {
        int left = findpostion(nums,target,true);
        int right = findpostion(nums,target,false)-1;
        if(left < nums.length &&nums[left] == target && nums[right]==target)
        {
            return right-left+1;
        }
        return 0;
    }
    public int findpostion(int[] nums,int target,boolean flag)
    {
        int left = 0,right = nums.length-1,mid=0,ans=0;
        while(left <= right)
        {
            mid = (left+right)/2;
            if(nums[mid] > target || (flag && nums[mid] >= target))
            {
                right = mid-1;
                ans = mid;
            }
            else
            {
                left = mid + 1;
            }
        }
        return left;
    }
}
```

这里面有个处理，通过一个布尔变量flag，从而可以分别找到最前面的值和最后面的值，减少了一个函数的编写。

## [剑指 Offer 53 - II. 0～n-1中缺失的数字](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/submissions/)

### 遍历

找到值不等于索引的项

时间复杂度O(n)，空间复杂度O(1)

```java
class Solution {
    public int missingNumber(int[] nums) {
        int ans=0;
        for(ans=0;ans<nums.length;ans++)
        {
            if(nums[ans]!=ans)
            {
                break;
            }
        }
        return ans;
    }
}
```

## [剑指 Offer 57. 和为s的两个数字](https://leetcode-cn.com/problems/he-wei-sde-liang-ge-shu-zi-lcof/)

前提是题目给的是升序数组

### 双指针

时间复杂度O(n),空间复杂度O(1)

left和right指针判断当前和与目标的大小关系，大于，right左移，小于，left右移。

~~~java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] ans = new int[2];
        int left =0,right=nums.length-1;
        while(left<=right)
        {
            int tmp = nums[left]+nums[right];
            if( tmp == target)
            {
                break;
            }
            else if(tmp > target)
            {
                right--;
            }
            else
            {
                left++;
            }
        }
        ans[0]=nums[left];
        ans[1]=nums[right];
        return ans;
    }
}
~~~

### 哈希

这种方法在数组是无序的时候也有用，因为hash里面查找target-e只需要一次查找即可。

时间复杂度O(n),空间复杂度O(n)

这个遍历了两次数组

~~~java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashSet<Integer> set = new HashSet<>();
        int[] ans = new int[2];
        for(int e : nums)
        {
            set.add(e);
        }
        for(int e : nums)
        {
            if(set.contains(e) && set.contains(target-e))
            {
                ans[0] = e;
                ans[1] = target-e;
                break;
            }
        }
        return ans;
    }
}
~~~

下面遍历一次数组，set添加过程和寻找过程结合在一起

~~~java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashSet<Integer> set = new HashSet<>();

        // 只需遍历一次数组
        for (int i = 0; i < nums.length; i++) {
            // 如果target-nums[i]在HashSet中，说明找到了
            if (set.contains(target - nums[i])) {
                return new int[]{nums[i], target-nums[i]};
            }
            // 否则要做的只是将当前元素添加到HashSet中
            set.add(nums[i]);
        }

        // 没有找到的话返回一个空数组
        return new int[0];
    }
}
~~~



### 二分查找

假如中间的值等于索引，说明缺失的数字不在左半部分，将left更新为mid，如果不等于索引，则证明缺失的数字在左半部分

时间复杂度O（logn）,空间复杂度O(1)

```java
class Solution {
    public int missingNumber(int[] nums) {
        int left =0,right=nums.length-1;
        while(left <= right)
        {
            int mid = (left+right)/2;
            if(nums[mid] == mid)
            {
                left = mid+1;
            }
            else
            {
                right = mid-1;
            }
        }
        return left;
    }
}
```



## [剑指 Offer II 069. 山峰数组的顶部](https://leetcode-cn.com/problems/B1IidL/)

### 遍历

遍历找到第一个arr[i]>arr[i+1]的位置，就是所要求的答案

~~~c++
class Solution {
public:
    int peakIndexInMountainArray(vector<int>& arr) {
        int n = arr.size();
        int ans = -1;
        for (int i = 1; i < n - 1; ++i) {
            if (arr[i] > arr[i + 1]) {
                ans = i;
                break;
            }
        }
        return ans;
    }
};
~~~

### 二分查找

目的是找到第一个arr[i]>arr[i+1]的位置，那么可以使用二分查找的办法，如果符合条件，往前面找。不符合找后半部分是否有符合条件的。

~~~c++
class Solution {
public:
    int peakIndexInMountainArray(vector<int>& arr) {
        int n = arr.size();
        int left = 1, right = n - 2, ans = 0;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (arr[mid] > arr[mid + 1]) {
                ans = mid;
                right = mid - 1;
            }
            else {
                left = mid + 1;
            }
        }
        return ans;
    }
};

~~~

