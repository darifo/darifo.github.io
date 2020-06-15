---
title: KMP算法
date: 2020-3-27 00:00:00
categories: 
- 算法
tags:
- 模式匹配
- KMP
---


KMP算法是一种改进的字符串匹配算法, 核心是利用匹配失败后的信息，尽量减少模式串与主串的匹配次数以达到快速匹配的目的。具体实现就是通过一个next()函数实现，函数本身包含了模式串的局部匹配信息。


关键点: **前缀集合、后缀集合、PMT(部分匹配表) , NEXT数组**;

（如 “abcd”，前缀集合：{a,ab,abc}，后缀集合：{bcd,cd,d}）


时间复杂度:  <font color=red>O(m+n)</font>  , m是主串大小, n是模式串大小

空间复杂度:  <font color=red>O(n)</font>  , 需要构造一个n大小的next数组



原理推算：

字符串S = “abcabcaba” 、模式串T = “bcaba”；

第一步，列出模式串 T 的子字符串的前后缀集合、公共集合(common)、公共串最大长度(value):

Prefix(Tb) = {} , Suffix(Tb) = {} ,Common(Tb) = {} ,value(Tb) = 0

Prefix(Tbc) = {b} , Suffix(Tbc) = {c} ,Common(Tbc) = {} ,value(Tbc) = 0

Prefix(Tbca) = {b,bc} , Suffix(Tbca) = {ca,a} ,Common(Tbca) = {} ,value(Tbca) = 0

Prefix(Tbcab) = {b,bc,bca} , Suffix(Tbcab) = {cab,ca,b} ,Common(Tbcab) = {b} ,value(Tbcab) = 1

Prefix(Tbcaba) = {b,bc,bca,bcab} , Suffix(Tbcaba) = {caba,aba,ba,a} ,Common(Tbcaba) = {} ,value(Tbcaba) = 0


因此，得到的 模式串T 的 PMT 如下：

char :   b c a b a

index:  0 1 2 3 4

pmt :    0 0 0 1 0

next :  -1 0 0 0 1


为了编程方便，这里把pmt的值整体往后移一位，首位置为-1，就得到对应的next数组；

其实，求next数组的过程完全可以看成字符串匹配的过程，即以模式字符串为主字符串，以模式字符串的前缀为目标字符串，一旦字符串匹配成功，那么当前的next值就是匹配成功的字符串的长度。


也可以这样看：

```
i = 0 ; j = -1
next[0] = -1;
next[1] = 0; // 第一位都为0 next[i] = j
next[2] = 0; // 这里 i = 1, j = 0 ,取出的 c 不等于 b , next数组第 2 位就为0；
   i = 1
b c a b a
   b c a b a
    j = 0

next[3] = 0; // 这里 i = 2, j = 0 ,取出的 a 不等于 b , next数组第 3 位就为0；
      i = 2
b c a b a
      b c a b a
      j = 0

next[4] = 0; // 这里 i = 3, j = 0 ,取出的 b 等于 b , next数组第 4 位就为 0,  j ++；
         i = 3
b c a b a
         b c a b a
         j = 0

next[5] = 1; // 这里 i = 4, j = 1 ,取出的 a 不等于 c , next数组第 5 位就为1；
            i = 4
b c a b a
         b c a b a
             j = 1
```

从上面的 next[4] 可以看出在 next[3] 匹配成功后，j 的位置从 0 到了 1，从而记录了当前位置之前已经连续匹配成功了 j 位字符；


总结：

next[i] = j; 

j = next[j];

当 i = 4, j = 1 匹配失败后，j 重置位置由next数组控制，即当前 j = next[1] = 0 ，如果说 j 的位置已经移动到了 2 ，即 j = 2，此时不匹配了，那么下次匹配 j 的位置就由 next[2] 的值决定； 


第二步，使用next数组进行比较，过程和上述求next数组相似；


伪代码：

**next**：

函数传入1参数 字符串P；

初始化next数组，next[0] = -1;

初始化 i = 0、j = -1；

循环开始，条件是 i 小于P的长度；

如果 j == -1 或者 P[i] == P[j] ，i++，j++，next[i] = j;

否则，j = next[j];

循环结束；

返回next数组;


**kmp**：

函数传入2参数，字符串S、字符串P；

初始化 i = 0、j = 0；

初始化next数组，调用next函数计算;

循环开始，条件是 i 小于S长度 并且 j 小于 P长度；

如果 j == -1 或者 S[i] = P[j], 那么 j++, i ++;

否则 j = next[j];

循环结束;

如果 j 等于P长度，返回 (i - j) 即查找结束，模式串在字符串中的位置；

否则 返回 -1 查找失败；


```java
package com.company.darifo.algorithms;

/* KMP算法（字符串匹配算法）
* KMP算法是BF(Brute Force)算法的一种改进算法
* 作用：快速查找 模式串P 在 主字符串S 中是否存在，存在并返回其在S中的位置下标
*
* 关键点：前缀、后缀、部分匹配表（PMT）、部分匹配值、NEXT数组、KMP算法
*
* @TODO 时间复杂度为O(M+N)
* */
public class KMP {

    private void getNext(String pattern, int[] next) {
        int j = 0;
        int k = -1;
        int len = pattern.length();
        next[0] = -1;

        while (j < len - 1) {
            if (k == -1 || pattern.charAt(k) == pattern.charAt(j)) {

                j++;
                k++;
                next[j] = k;
            } else {

                // 比较到第K个字符，说明p[0——k-1]字符串和p[j-k——j-1]字符串相等，而next[k]表示
                // p[0——k-1]的前缀和后缀的最长共有长度，所接下来可以直接比较p[next[k]]和p[j]
                k = next[k];
            }
        }

    }

    public boolean hasSubstring(char[] text, char[] pattern){
        int i=0;
        int j=0;
        int k = 0;
        while(i < text.length && j < pattern.length){
            if(text[i] == pattern[j]){
                i++;
                j++;
            }else{
                j=0;
                k++;
                i = k;
            }
        }
        return j == pattern.length;
    }

    public int kmp(String s, String pattern) {
        int i = 0;
        int j = 0;
        int slen = s.length();
        int plen = pattern.length();

        int[] next = new int[plen];

        getNext(pattern, next);

        while (i < slen && j < plen) {

            if (s.charAt(i) == pattern.charAt(j)) {
                i++;
                j++;
            } else {
                if (next[j] == -1) {
                    i++;
                    j = 0;
                } else {
                    j = next[j];
                }

            }

            if (j == plen) {
                return i - j;
            }
        }
        return -1;
    }
/*    public static void main(String[] args) {
        // TODO Auto-generated method stub
        KMP kmp = new KMP();
        String str = "abababdafdasabcfdfeaba";
        String pattern = "abc";
        System.out.println(kmp.kmp(str, pattern));
    }*/
}

```