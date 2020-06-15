---
title: BF（Brute Force）模式匹配算法
date: 2020-3-26 00:00:00
categories: 
- 算法
tags:
- 模式匹配
- BF算法
---


暴力(Brute Force)算法, 是普通的模式匹配算法｡

思想: 将目标串 S 的第一个字符与模式串 T 的第一个字符进行匹配,若相等,则继续比较 S 的第二个字符和 T 的第二个字符;若不相等,则比较 S 的第二个字符和 T 的第一个字符, 依次比较下去,直到得出最后的匹配结果｡

时间复杂度:  <font color=red>O(m*n)</font>  m是S的长度  n是T的长度

空间复杂度:  <font color=red>O(1)</font>


伪代码:

函数开始, 传入2个参数字符串 S､字符串T;

初始化字符串数组 A､B , 分别存储 S､T ;

初始化整型指针 i = 0､ j = 0,意味着分别指向 A､ B 的首元素位置;

循环体开始,条件是 i 小于 A 的长度, j 小于 B 的长度;

如果 A[i] 等于 B[j] , i 加 1, j 加 1｡ 

否则 i = i - j + 1, j 等于 0;

循环体结束;

如果 j 等于 B 的长度, 说明比较完了,函数返回 ( i - j ) 就是 T 在 S 中的位置;

否则返回 -1 表示查找失败;


```java
package com.company.darifo.algorithms;

/*
* BF算法，即暴力(Brute Force)算法，是普通的模式匹配算法，
* BF算法的思想就是将目标串S的第一个字符与模式串T的第一个字符进行匹配，
* 若相等，则继续比较S的第二个字符和 T的第二个字符；
* 若不相等，则比较S的第二个字符和T的第一个字符，依次比较下去，直到得出最后的匹配结果。BF算法是一种蛮力算法。
*
* @todo 时间复杂度为O(M*N)
* */
public class BF {

    public static int bfFind(String S, String T, int pos) {
        char[] arr1 = S.toCharArray();
        char[] arr2 = T.toCharArray();
        int i = pos;
        int j = 0;
        while(i < arr1.length && j < arr2.length) {
            if(arr1[i] == arr2[j]) {
                i++;
                j++;
            }
            else {
                i = i - j + 1;
                j = 0;
            }
        }
        if(j == arr2.length) return i - j;
        else return -1;
    }
}
```
