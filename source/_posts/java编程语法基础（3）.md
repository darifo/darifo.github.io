---
title: java编程语法基础（3）
categories: 
- 编程语言
tags:
- JAVA
- 基础语法
- 后端开发
---


# 1、基本数据类型

Java 的两大数据类型:

- 内置数据类型
- 引用数据类型

## 内置数据类型

Java语言提供了八种基本类型。六种数字类型（四个整数型，两个浮点型），一种字符类型，还有一种布尔型。

**byte**：

byte 数据类型是<font color=red> 8 </font>位、有符号的，以二进制补码表示的整数；

最小值是 -128（-2^7）；

最大值是 127（2^7-1）；

默认值是 0；

byte 类型用在大型数组中节约空间，主要代替整数，因为 byte 变量占用的空间只有 int 类型的四分之一；

例子：byte a = 100，byte b = -50。


**short**：

short 数据类型是 <font color=red> 16 </font> 位、有符号的以二进制补码表示的整数

最小值是 -32768（-2^15）；

最大值是 32767（2^15 - 1）；

Short 数据类型也可以像 byte 那样节省空间。一个short变量是int型变量所占空间的二分之一；

默认值是 0；

例子：short s = 1000，short r = -20000。

**int**：

int 数据类型是<font color=red> 32 </font>位、有符号的以二进制补码表示的整数；

最小值是 -2,147,483,648（-2^31）；

最大值是 2,147,483,647（2^31 - 1）；

一般地整型变量默认为 int 类型；

默认值是 0 ；

例子：int a = 100000, int b = -200000。

**long**：

long 数据类型是 <font color=red>64</font> 位、有符号的以二进制补码表示的整数；

最小值是 -9,223,372,036,854,775,808（-2^63）；

最大值是 9,223,372,036,854,775,807（2^63 -1）；

这种类型主要使用在需要比较大整数的系统上；

默认值是 0L；

例子： long a = 100000L，Long b = -200000L。

"L"理论上不分大小写，但是若写成"l"容易与数字"1"混淆，不容易分辩。所以最好大写。

**float**：

float 数据类型是单精度、<font color=red> 32 </font> 位、符合IEEE 754标准的浮点数；

float 在储存大型浮点数组的时候可节省内存空间；

默认值是 0.0f；

浮点数不能用来表示精确的值，如货币；

例子：float f1 = 234.5f。

**double**：

double 数据类型是双精度、<font color=red> 64 </font> 位、符合IEEE 754标准的浮点数；

浮点数的默认类型为double类型；

double类型同样不能表示精确的值，如货币；

默认值是 0.0d；

例子：double d1 = 123.4。

**boolean**：

boolean数据类型表示一位的信息；

只有两个取值：true 和 false；

这种类型只作为一种标志来记录 true/false 情况；

默认值是 false；

例子：boolean one = true。

**char**：

char类型是一个单一的 <font color=red>16</font> 位 Unicode 字符；

最小值是 \u0000（即为0）；

最大值是 \uffff（即为65,535）；

char 数据类型可以储存任何字符；

例子：char letter = 'A';。



代码例子：

```java
public class BaseType {

    static boolean bool;
    static byte by;
    static char ch;
    static double d;
    static float f;
    static int i;
    static long l;
    static short sh;

    public static void main(String[] args){

        // 默认值
        System.out.println("Bool :" + bool);
        System.out.println("Byte :" + by);
        System.out.println("Character:" + ch);
        System.out.println("Double :" + d);
        System.out.println("Float :" + f);
        System.out.println("Integer :" + i);
        System.out.println("Long :" + l);
        System.out.println("Short :" + sh);

        builtInDataTypes();
        referenceDataTypes();
    }
    /*
    * todo 八大 内置数据类型 4个整型 2个浮点型 1个字符型 1个布尔型
    * */
    private static void builtInDataTypes(){
        System.out.println("说明 \t byte \t int \t short \t long \t float \t double \t char");
        int[] typeSizeArray = {
                Byte.SIZE,
                Integer.SIZE,
                Short.SIZE,
                Long.SIZE,
                Float.SIZE,
                Double.SIZE,
                Character.SIZE
        };
        System.out.print("长度： \t ");
        for (int l:typeSizeArray){
            System.out.print(l + " \t ");
        }

        System.out.print("\n最小值： \t ");
        System.out.print(Byte.MIN_VALUE + " \t ");
        System.out.print(Integer.MIN_VALUE + " \t ");
        System.out.print(Short.MIN_VALUE + " \t ");
        System.out.print(Long.MIN_VALUE + " \t ");
        System.out.print(Float.MIN_VALUE + " \t ");
        System.out.print(Double.MIN_VALUE + " \t ");

        System.out.print("\n最大值： \t ");
        System.out.print(Byte.MAX_VALUE + " \t ");
        System.out.print(Integer.MAX_VALUE + " \t ");
        System.out.print(Short.MAX_VALUE + " \t ");
        System.out.print(Long.MAX_VALUE + " \t ");
        System.out.print(Float.MAX_VALUE + " \t ");
        System.out.print(Double.MAX_VALUE + " \t ");
    }
}

```

输出：
```
Bool :false
Byte :0
Character: 
Double :0.0
Float :0.0
Integer :0
Long :0
Short :0
说明 	 byte 	 int 	 short 	 long 	 float 	 double 	 char
长度： 	 8 	 32 	 16 	 64 	 32 	 64 	 16 	 
最小值： 	 -128 	 -2147483648 	 -32768 	 -9223372036854775808 	 1.4E-45 	 4.9E-324 	 
最大值： 	 127 	 2147483647 	 32767 	 9223372036854775807 	 3.4028235E38 	 1.7976931348623157E308 
```


## 引用数据类型


引用类型指向一个变量，指向对象的变量是引用变量。声明时被指定特定类型。

对象，数组都是引用类型。

所有引用类型默认值都是null。

引用变量可以兼容类型。


```java
    /*
    * todo 引用数据类型
    * */
    private static void referenceDataTypes(){

        // BaseType这就是引用类型
        BaseType baseType = new BaseType();
    }
```


# 2、JAVA常量


常量在程序运行时是不能被修改的。

在 Java 中使用 final 关键字来修饰常量。

虽然常量名也可以用小写，但为了便于识别，通常使用大写字母表示常量。

字符串常量和字符常量都可以包含任何Unicode字符。


```java
    /*
    * todo 常量
    * */
    private static void cons(){
        final int A = 0;
    }
```


## 转义字符

![](/images/20200326120950.gif)



## 自动类型转换

整型、实型（常量）、字符型数据可以混合运算。运算中，不同类型的数据先转化为同一类型，然后进行运算。

从低级到高级:

byte,short,char—> int —> long—> float —> double


规则：

- 不能对boolean类型进行类型转换。

- 不能把对象类型转换成不相关类的对象。

- 在把容量大的类型转换为容量小的类型时必须使用强制类型转换。

- 转换过程中可能导致溢出或损失精度


```java
    char c1='a'; //定义一个char类型
    int i1 = c1; //char自动类型转换为int
    System.out.println("char自动类型转换为int后的值等于" + i1);
    char c2 = 'A'; //定义一个char类型
    int i2 = c2 + 1; //char 类型和 int 类型计算
    System.out.println("char类型和int计算后的值等于" + i2);


char自动类型转换为int后的值等于97
char类型和int计算后的值等于66

```


## 强制类型转换

```java
    int i1 = 123;
    byte b = (byte) i1; //强制类型转换为byte
    System.out.println("int强制类型转换为byte后的值等于" + b);

int强制类型转换为byte后的值等于123
```
