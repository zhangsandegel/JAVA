

[TOC]

# 01----19删除链表的倒数第 N 个结点

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311164841959.png" alt="image-20220311164841959" style="zoom:80%;" />

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311164938971.png" alt="image-20220311164938971" style="zoom:80%;" />

**📖 文字题解**

## 1、前言

在对链表进行操作时，一种常用的技巧是添加一个**==哑节点（dummy node）==**，它的next 指针指向链表的头节点。这样一来，我们就不需要对头节点进行特殊的判断了。

```properties
例如，在本题中，如果我们要删除节点 y，我们需要知道节点 y 的前驱节点 x，并将 x 的指针指向 y 的后继节点。但由于头节点不存在前驱节点，因此我们需要在删除头节点时进行特殊判断。但如果我们添加了哑节点，那么头节点的前驱节点就是哑节点本身，此时我们就只需要考虑通用的情况即可。
```

特别地，在某些语言中，由于需要自行对内存进行管理。因此在实际的面试中，对于「**是否需要释放被删除节点对应的空间」**这一问题，我们需要和面试官进行积极的沟通以达成一致。下面的代码中默认不释放空间。

## 2、方法一：暴力解法

思路与算法

一种容易想到的方法是，我们首先从头节点开始对链表**进行一次遍历**，得到链表的长度 L。随后我们再从头节点开始对链表**进行一次遍历**，当遍历到第 L-n+1个节点时，它就是我们需要删除的节点。

为了与题目中的 n保持一致，节点的编号从 1开始，头节点为编号 1 的节点。

为了方便删除操作，我们可以从哑节点开始遍历 L-n+1 个节点。**当遍历到第 L-n+1个节点时，它的下一个节点就是我们需要删除的节点，这样我们只需要修改一次指针，就能完成删除操作**。

Java代码：

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        // 定义一个哑结点，上一个节点指向
        ListNode dummy = new ListNode(0, head);
        // 定义一个函数，获取链表的长度，进行的第一次遍历
        int length = getLength(head);
        //从哑结点开始遍历 
        ListNode cur = dummy;
        //第二次遍历，到第L-n+1个节点的前一个节点的时候
        for (int i = 1; i < length - n + 1; ++i) {
            cur = cur.next;
        }
     	// 删除第L-n+1个节点
        cur.next = cur.next.next;
        // 返回链表的头结点
        ListNode ans = dummy.next;
        return ans;
    }
	
    // 获取整个链表的长度
    public int getLength(ListNode head) {
        int length = 0;
        while (head != null) {
            ++length;
            head = head.next;
        }
        return length;
    }
} 
```

复杂度分析

- 时间复杂度：O(L)，其中 L是链表的长度。

- 空间复杂度：O(1)。

  <img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311171003383.png" alt="image-20220311171003383" style="zoom:67%;" />

## 2、方法二：栈

思路与算法

我们也可以在**遍历链表的同时将所有节点依次入栈**。根据栈「先进后出」的原则，我们弹出栈的第 n 个节点就是需要删除的节点，并且目前栈顶的节点就是待删除节点的前驱节点。这样一来，删除操作就变得十分方便了。

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311165634233.png" alt="image-20220311165634233" style="zoom:80%;" />

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311165650497.png" alt="image-20220311165650497" style="zoom:67%;" />

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311165716435.png" alt="image-20220311165716435" style="zoom:67%;" />

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311165730474.png" alt="image-20220311165730474" style="zoom:67%;" />

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311165743633.png" alt="image-20220311165743633" style="zoom:67%;" />

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311165756228.png" alt="image-20220311165756228" style="zoom:67%;" />

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311165809587.png" alt="image-20220311165809587" style="zoom:67%;" />

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311165825258.png" alt="image-20220311165825258" style="zoom:67%;" />

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311165839682.png" alt="image-20220311165839682" style="zoom:67%;" />

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311165852145.png" alt="image-20220311165852145" style="zoom:67%;" />

Java代码：

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        //定义一个哑结点
        ListNode dummy = new ListNode(0, head);
        // 定义一个 栈
        Deque<ListNode> stack = new LinkedList<ListNode>();
        ListNode cur = dummy;
        
        //将所有的节点，压栈
        while (cur != null) {
            stack.push(cur);
            cur = cur.next;
        }
        
        // 弹出栈的第 n 个节点就是需要删除的节点
        for (int i = 0; i < n; ++i) {
            stack.pop();
        }
        
        // 目前栈顶的节点就是待删除节点的前驱节点
        ListNode prev = stack.peek();
        prev.next = prev.next.next;
        ListNode ans = dummy.next;
        return ans;
    }
} 
```

复杂度分析

- 时间复杂度：O(L)，其中 L是链表的长度。
- 空间复杂度：O(L)，其中 L 是链表的长度。主要为栈的开销。

## 3、方法三：快慢指针

![image-20220311171127878](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311171127878.png)

**思路与算法**

我们也可以在不预处理出链表的长度，以及使用常数空间的前提下解决本题。

由于我们需要找到倒数第 n 个节点，**因此我们可以使用两个指针first 和second 同时对链表进行遍历，并且 first 比second 超前 n个节点。当first 遍历到链表的末尾时，second 就恰好处于倒数第 n个节点。**

具体地，初始时 first 和 second 均指向头节点。我们首先使用first 对链表进行遍历，遍历的次数为 n。此时，first 和second 之间间隔了 n-1 个节点，即first 比second 超前了 n个节点。

在这之后，我们同时使用first 和second 对链表进行遍历。当first 遍历到链表的末尾（即first 为空指针）时，second 恰好指向倒数第 n个节点。

根据方法一和方法二，如果我们能够得到的是倒数第 n个节点的前驱节点而不是倒数第 n 个节点的话，删除操作会更加方便。因此我们可以考虑在初始时将second 指向哑节点，其余的操作步骤不变。这样一来，当 first 遍历到链表的末尾时，second 的下一个节点就是我们需要删除的节点。

![image-20220311171039484](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311171039484.png)

![p3](https://assets.leetcode-cn.com/solution-static/19/p3.png)

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0, head);
        ListNode first = head;
        ListNode second = dummy;
        for (int i = 0; i < n; ++i) {
            first = first.next;
        }
        while (first != null) {
            first = first.next;
            second = second.next;
        }
        second.next = second.next.next;
        ListNode ans = dummy.next;
        return ans;
    }
} 
```

**复杂度分析**

- 时间复杂度：O(L)，其中 L*L* 是链表的长度。
- 空间复杂度：O(1)。