# 数组与指针

> 录制文件: [record/talk-2.mp4](https://cfile.thudep.com:6443/record/talk-2.mp4)

## 数组

数组是多数编程语言中的基本数据类型之一, 用来连续存储相同类型的数据. 数组的元素可以通过下标来访问, 下标从 `0` 开始.

```c
int a[5] = {1, 2, 3, 4, 5};
struct Point {
    int x;
    int y;
};
struct Point points[3] = {{1, 2}, {3, 4}, {5, 6}};
```

访问任何一个元素的时间复杂度是 $O(1)$. 数组的长度是固定的, 不能动态改变.

访问越界的数组元素是**未定义行为**(undefined behavior, **UB**), 可能会导致程序崩溃.

```c
int a[5] = {1, 2, 3, 4, 5};
printf("%d\n", a[5]); // 未定义行为
```

## 指针

### 指针是值为内存地址的变量

每一个变量都有一个内存位置, 每一个内存位置都定义了可使用 `&` 运算符访问的地址, 它表示在虚拟内存中的一个地址.

例如, 变量 `a` 的地址是 `0x7fffbf7f1b4c`, 可以通过 `&a` 来获取 `0x7fffbf7f1b4c` 这个值.

在 64 位机器中, 指针的大小是 8 字节, 在 32 位机器中, 指针的大小是 4 字节. 指针的大小就是机器的字长.

`int` 指针的类型声明是 `int *`, 表示指向 `int` 类型的指针. 类似地, `char` 指针的类型声明是 `char *`, 表示指向 `char` 类型的指针.

例如, 通过指针加偏移量来访问数组元素:

```c
int a[5] = {1, 2, 3, 4, 5};
int *p = a;               // 定义指针 p, p 指向 a 的第一个元素
printf("%d\n", *p);       // 输出 1
printf("%d\n", *(p + 1)); // 输出 2
```

> 通过下标访问数组元素和通过指针访问数组元素是等价的. 可以查看 [godbolt](https://godbolt.org/) 来查看编译器生成的汇编代码.

指针还可以指向指针, 例如, `int **` 表示指向 `int *` 类型的指针.

可以用这样的方式动态创建二维数组:

```c
int m = 3, n = 4;
int **a = (int **)malloc(n * sizeof(int *));
for (int i = 0; i < n; i++) {
    a[i] = (int *)malloc(m * sizeof(int));
}
int *p = a[0];            // p 指向 a 的第一个元素
printf("%d\n", *p);       // 输出 1
printf("%d\n", *(p + 1)); // 输出 2
for (int i = 0; i < n; i++) {
    free(a[i]);
}
free(a);
```

### 通过指针修改变量的值

对指针使用 `*` 解引用操作符, 可以获取和修改修改变量的值, 例如:

```c
#include <stdio.h>
void swap(int *a, int *b) {
    printf("%d %d\n", *a, *b); // 输出 1 2
    int t = *a;
    *a = *b;
    *b = t;
}
int main() {
    int x = 1, y = 2;
    swap(&x, &y);
    printf("%d %d\n", x, y); // 输出 2 1
    return 0;
}
```

### 动态内存分配

在 C 语言中, 可以使用 [`malloc`](https://en.cppreference.com/w/c/memory/malloc) 函数来动态分配内存, 使用 [`free`](https://en.cppreference.com/w/c/memory/free) 函数来释放内存.

它们的原型在 `stdlib.h` 头文件中, 在典型的 Linux 操作系统中, 具体的实现由 `glibc` 提供.

```c
#include <stdlib.h>
#include <stdio.h>
int main() {
    int *p = (int *)malloc(5 * sizeof(int));
    for (int i = 0; i < 5; i++) {
        printf("%d\n", p[i]);
    }
    free(p); // 释放内存

    int *q = (int *)calloc(5, sizeof(int)); // 并初始化为 0
    for (int i = 0; i < 5; i++) {
        printf("%d\n", q[i]);
    }
    free(q);
}
```

> `malloc` 与 `calloc` 的区别是, `malloc` 不会初始化内存, `calloc` 会初始化内存为 0.
>
> 此外, `malloc` 接受的参数是分配的字节数, `calloc` 接受的参数是分配的元素个数和每个元素的大小.
