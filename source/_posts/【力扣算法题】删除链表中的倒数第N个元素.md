---
title: 【力扣算法题】删除链表中的倒数第N个元素
author: Darifo
Github: 'https://github.com/darifo'
Pages: 'https://darifo.github.io/'
date: 2020-04-11 12:11:47
categories:
- LeetCode
tags:
- 链表
- 数据结构
- 算法
---


给定一个链表，删除链表的**倒数第 n 个节点**，并且返回链表的头结点。

示例：

给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.

说明：

给定的 n 保证是有效的。

进阶：

你能尝试使用一趟扫描实现吗？



### 解决


- 自己手画了一个图，凑合看着理解数据结构先。。。

![](/images/20200411144810.png)


链表操作的题目，一般都需要有指针（一个或者两个），通过移动指针指向，控制元素的增加、删除等操作

解决方法有很多种，比如：两遍指针（需要先计算链表长度L）、一遍指针（双指针同时移动）等

#### 步骤 ( 比较好的算法实现——移动一遍指针 )

- 链表输入（给定头结点指针 head， 以及移除位置n）
- 定义一个指向头结点的节点指针【重要】（h.next = head），为什么要多增加一个节点指向，因为不管对链表进行何种操作，我们的链表头是不能丢失的，否则就找不到数据入口，没有了意义，所以要么多增加一个节点指向头，要么用临时变量操作前存储头指针，总之不能丢失。
- 定义节点指针：定义两个节点指针（左指针、右指针），都指向头部节点（p.next = head）
- 先移动 右指针 ，移动步数 由 输入的 n 决定（本题，需要移除倒数第 n 个位置的节点，我们就先把右指针往右边移动 n + 1 步，这样 右指针和左指针之间的距离就刚好是 n 个节点）
- 然后，我们再 同步移动 两个指针（p = p.next），当 右指针 为 null 时，停止移动，此时，左指针的下一个节点指向刚好就是 倒数第 n 个节点。
- 最后，我们操作倒数第 n 个 节点的移除（p.next = p.next.next），意思就是把左指针 下一个节点 指向 了 下下个节点，即跳过了倒数第 n 个节点。
- 返回头节点（h.next）结束


#### 完整代码实现

- 时间复杂度： O(L) L为链表长度
- 空间复杂度： O(1)

链表结构

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) {
        val = x;
    }
}
```

处理类


```java
package com.company.darifo.solutions;
import java.util.Scanner;
public class RemoveNthNode {
    static ListNode headNode = null;
    static ListNode tmp = null;
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入链表长度：");
        int len = scanner.nextInt();
        System.out.println("请输入头结点值：");
        headNode = new ListNode(scanner.nextInt());
        tmp = headNode;
        for (int i = 1; i < len; i++) {
            System.out.println("请输入节点值: ");
            headNode.next = new ListNode(scanner.nextInt());
            headNode = headNode.next;
        }
        // 打印输入
        printList(tmp);

        // 移除 倒数 第 n 个节点 n 不能大于链表长度 否则空指针异常
        System.out.println("移除倒数第 ? 个节点: ");
        int n = scanner.nextInt();
        for(int j = 2;j<=3;j++){
            if ( n < len)
                break;
            System.out.println("输入有误，第 " + j + "次输入：");
            n = scanner.nextInt();
        }
        if ( n > len){
            System.out.println("三次输入错误！退出");
            System.exit(-1);
        }

        // 执行操作
        ListNode head = removeNthFromEnd(tmp, n);
        // 打印结果
        printList(head);

        scanner.close();
    }

    private static void printList(ListNode node) {
        System.out.print("\n");
        while (node != null) {
            System.out.print(node.val + "->");
            node = node.next;
        }
        System.out.print("null");
        System.out.print("\n");
    }

    /**
    * 算法函数
    */
    private static ListNode removeNthFromEnd(ListNode head, int n) {
        // 定义两个节点指针，分别指向头结点和头结点的下一节点
        ListNode h = new ListNode(0);
        h.next = head;
        ListNode l = h;
        ListNode r = h;
        // 首先移动右边指针 r 使其移动 N + 1 位，此时 r 和 l 之间间隔 就是 N
        for (int i = 1; i <= n + 1; i++) {
            r = r.next;
        }
        // 然后，同时向右移动两个指针，使 r 指针 next 为 null 时终止，此时，l 指针的下一个节点就是 倒数第N个节点
        while (r != null) {
            r = r.next;
            l = l.next;
        }
        // 移除节点
        l.next = l.next.next;
        return h.next;
    }
}
```




来源：

力扣（LeetCode）链接：https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list


