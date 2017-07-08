---
title: lab1_fitsBits
---

```c++
/*
* fitsBits - return 1 if x can be represented as an 
*  n-bit, two's complement integer.
*   1 <= n <= 32
*   Examples: fitsBits(5,3) = 0, fitsBits(-4,3) = 1
*   Legal ops: ! ~ & ^ | + << >>
*   Max ops: 15
*   Rating: 2
*/
int fitsBits(int x, int n) {
    int r, c;
    c = 33 + ~n;  //c = 33 + ~n = 33 + (-n - 1) = 32 - n
    r = !(((x << c)>>c)^x);
    return r;
}
```

### 解题思路
> 首先，第一个数字代表要表示的数，第二个数字表示可用的位数。
> 然后，我们把这个n-bit之外的bit截断，假如没有丢失bit，那么就说明没有问题。

### 演示
5	= 00000000 00000000 00000000 00000101
-4	= 11111111 11111111 11111111 11111100

> 我们想象从前面往后面截断

5	= 00000000 00000101
-4	= 11111111 11111100

> 继续截断

5	= 00000101
-4	= 11111100

> 再截断

5	= 0101
-4	= 1100

> 此时5和-4在4-bit two's complement 之下都是原本的语意
> 但是再截断一位呢

5	= 101 #此时5丢失了原来的语意，在 3-bit two's complement 之下变成了 -3
-4	= 100 # 4依然保持语意

> 根据归纳法，只需要判断截断后的数字和原来的数字在不同的 n-bit two's complement 之下是否表达同样的语意。
> 根据观察，我们发现，将判断条件改为截断后bit位没有丢失信息即可。
> 先左移，后右移即可完成截断操作。
> 当然这里针对x的是算数右移。

### plus
fitsBits(-2147483648[0x80000000],32[0x20]) failed...
...Gives 1[0x1]. Should be 0[0x0]
这里大家可以忽略

### 我怎么做出来？
纸上慢慢划，然后再网上查资料
http://blog.csdn.net/u014247371/article/details/46288913


