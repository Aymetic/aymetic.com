---
title: C++实现的几种斐波那契数列算法
math: true
katex: true
categories:
 - 算法
tags:
  - 算法
  - C++
abbrlink: 57a23e5f
date: 2021-02-10 23:06:09
---

作为算法课的练手项目，斐波那契数列的生成再合适不过了。最重要的就是蛮力法，可以拿来求解斐波那契数列并可以进一步的优化求解。



## 经典的迭代和递归

斐波那契数列有关十分明显的特点，那是：前面相邻两项之和，构成了后一项。即$F(n)=F(n-1)+F(n-2), F(0)=0, F(1)=F(2)=1$, 推导下去$F(3)=2, F(4)=3, F(5)=5...$由此可以很容易得到下面的代码：

```cpp
//递归算法
int Recursion(int n)
{
    if (n <= 1)
        return n;
    return Recursion(n - 1) + Recursion(n - 2);
}
//迭代算法
int Iteration(int n)
{
    int *F = new int[n + 1];
    if (n <= 1)
        return n;
    F[0] = 0;
    F[1] = 1;
    for (int i = 2; i <= n; ++i)
        F[i] = F[i - 1] + F[i - 2];
    int result = F[n];
    delete[] F;
    return result;
}
```

> 使用上面的方式求解第n项斐波那契数列的时间复杂度为**O(n)**,也就是说，时间复杂度随着n的增长而线性增长。

## 用矩阵乘法做优化

### 矩阵的概念

在数学中，矩阵（Matrix）是一个按照长方阵列排列的复数或实数集合，最早来自于方程组的系数及常数所构成的方阵。

矩阵相乘：矩阵相乘最重要的方法是一般矩阵乘积。它只有在第一个矩阵的列数（column）和第二个矩阵的行数（row）相同时才有意义。一般单指矩阵乘积时，指的便是一般矩阵乘积。一个m×n的矩阵就是m×n个数排成m行n列的一个数阵。由于它把许多数据紧凑的集中到了一起，所以有时候可以简便地表示一些复杂的模型。

设A为m\*p的矩阵，B为p\*n的矩阵，那么称m\*n的矩阵C为矩阵A与B的乘积，记作C=AB

### 求解过程

前面提到说从第三项开始，往后的每一项都是前面两项之和，即$F(n)=F(n-1)+F(n-2), n≥3$. 我们将数列的相邻的两项表示为下列矩阵
$$
\begin{bmatrix}
F(n+1)&F(n)\\
F(n)&F(n-1)\\
\end{bmatrix}
=
{\begin{bmatrix}
1&1\\
1&0\\
\end{bmatrix}}^n
$$
这里有个需要注意的地方就是如果要求的幂是2的幂次数还比较好算，如果不是的话我们就需要对其做一些判断和处理。判断要求的幂的二进制数和1相与，查看结果是否为1，如果为1说明这个数不是2的整数次幂，需要一次一次的求矩阵的幂，直到这个数是2的整数次幂。

上代码：

```cpp
//矩阵乘法
vector<vector<int>> MatrixMulti(vector<vector<int>> &a, vector<vector<int>> &b)
{
    vector<vector<int>> res;
    res.push_back({0, 0});
    res.push_back({0, 0});
    for (int i = 0; i < 2; ++i)
        for (int j = 0; j < 2; ++j)
            for (int k = 0; k < 2; ++k)
                res[i][j] += a[i][k] * b[k][j];

    return res;
}
int Matrix(int n)
{
    vector<vector<int>> result;
    vector<vector<int>> x;
    result.push_back({1, 0});
    result.push_back({0, 1});
    x.push_back({1, 1});
    x.push_back({1, 0});
    x = MatrixMulti(result,x);

    while (n)
    {
        if(n & 1) //取n的二进制的最后一位和1做与运算，如果最后一位是1，则进入if体内部
            result = MatrixMulti(result,x); //如果在该位置n的二进制为1，则计算result和x矩阵
        x = MatrixMulti(x,x); //x矩阵相乘，相当于初始x矩阵的幂*2
        //printVector(x);
        n /= 2;
    }

    return result[0][1]; //最后获取到的二阶矩阵的[0][1]即F(n)的值
}
```

> 使用上面的方式求解第n项斐波那契数列的时间复杂度是**O(logn)**

## 公式法

公式法没啥好说的，直接上个求解斐波那契数列的公式：
$$
F(n)=\frac {1}{\sqrt 5}[{(\frac {1+\sqrt 5}{2})}^n-{(\frac {1-\sqrt 5}{2})}^n]
$$
上代码：

```cpp
int Formula(int n)
{
    return (1.0 / sqrt(5)) * (pow(((1 + sqrt(5)) / 2.0), n) - (-1.0 / pow(((1 + sqrt(5)) / 2.0), n)));
}
```

> 使用上面的方式求解第n项斐波那契数列的时间复杂度是**O(1)**

## 优化迭代算法

此算法将迭代算法从O(n)优化成O(1)，且代码思路非常简单！

直接上代码，一看就明白

```cpp
int BetterIteration(int n)
{
    int a = 0, b = 1;
    for (int i = 1; i < n; ++i)
    {
        if (i % 2 == 1)
            a = a + b;
        else
            b = a + b;
    }
    return a > b ? a : b;
}
```

> 使用上面的方式求解第n项斐波那契数列的时间复杂度是**O(1)**

## 总结

抛开最前面的暴力递归和迭代，思路过于简单且效率低下，剩下的都是经过优化后的算法。由此可见，蛮力法往往是我们首先想到的开思路的算法。但是经过我们一步一步的演化分析，最终我们得到了复杂度为常数的算法！这是非常令人振奋的事！