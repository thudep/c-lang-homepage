# 第二次培训: 编写更复杂的程序

> 查看第二次培训的[回放与文件](https://cfile.thudep.com:7100/11/talk2).

## 指针

### 基本知识

#### 定义

**指针**也就是内存地址, 指针变量是用来存放内存地址的变量, 就像其他变量或常量一样, 必须在使用指针存储其他变量地址之前, 对其进行**声明**. 指针变量声明的一般形式为:
```c
type *var_name;
```
在这里, `type` 是指针的基类型, 它必须是一个有效的 C 数据类型, `var_name` 是指针变量的名称, 例如:
```c
int* p1;  // int 类型的指针
double *p2, *p3; // 两个 double 类型的指针
char *p4, c5; // 一个 char 类型的指针, 一个 char 类型的变量
```
所有实际数据类型, 对应指针的值都是一个**代表内存地址的长的十六进制数**, 对于 64 位的系统, 占 `8` 字节. 不同数据类型的指针之间唯一的不同是, 指针所指向的变量或常量的数据类型不同. 绝大多数现代通用计算机系统都是**小端系统**, 即内存地址**从低到高**排布.

#### 使用

使用指针时会涉及以下几个操作:
- 声明一个指针变量;
- 把变量地址赋值给指针 (通过取地址运算符 `&` );
- 访问指针变量中可用地址的值.

一个例子是:
```c
// pointer.c
#include <stdio.h>
int main(){
    int a = 20;
    int *p;
    p = &a;
    printf("the address of a: %p\n", &a);
    printf("the value of p: %p\n", p);
    printf("the size of p: %zu\n", sizeof(p));
    printf("the value of *p: %d\n", *p);
    return 0;
}
```
*注: 可以尝试注释掉 `p = &a;` 一句, 观察未初始化的指针值.*

如果要修改变量的值, 既可以直接修改, 也可以修改指针指向的值, 二者会同步变动, 例如: 
```c
// pointer_change.c
#include <stdio.h>
int main(){
    int a = 20;
    int *p = &a;
    printf("a, *p:  %d, %d\n", a, *p);
    a = 30;
    printf("a, *p:  %d, %d\n", a, *p);
    *p = 10;
    printf("a, *p:  %d, %d\n", a, *p);
    return 0;
}
```

多个指针可以指向同一个变量, 例如:
```c
// pointers.c
#include <stdio.h>
int main(){
    short a = 1 << 7;
    short *p1 = &a, *p2 = &a;
    char *pc = &a;
    int *pi = &a;
    printf("*p1, *p2:  %d, %d\n", *p1, *p2);
    printf("*pc:  %d\n", *pc);
    printf("*pi:  %d\n", *pi);
    return 0;
}
```
*思考: `*pc` 与 `*pi` 的值各是什么含义.*

#### 空指针 `NULL`

**空指针** `NULL` 是一个定义在 `<stddef.h>` 库中的值为零的常量:
```c
#define NULL ((void *)0)
```

通过以下代码可以查看 `NULL` 的值:
```c
// NULL.c
#include <stdio.h>
int main(){
    int *p = NULL;
    printf("the value of p (%%p): %p\n", p);
    printf("the value of p (%%u): %u\n", p);
    printf("the value of *p: %d\n", *p);
    return 0;
}
```

`NULL` 指向的地址的的值是不可访问的, 因此如果一个指针不用了, 或者在其值的修改过程中出现了错误, 可以选择将其值置为 `NULL` . 可以参考以下方法判断指针是否为空:
```c
// judge_NULL.c
#include <stdio.h>
void judge(int *p){
    if(p) printf("%p\n", p);
    if(!p) printf("NULL\n");
}
int main(){
    int a = 1;
    int *p1 = &a, *p2 = NULL;
    judge(p1);
    judge(p2);
    return 0;
}
```

#### 运算

指针可以进行**加减运算**, 即支持以下四种运算符: `+`, `-`, `++`, `--`. 不过, 指针的加减运算与一般的 C 数据类型不同, 对于基类型为 `type` 的指针, 其加减的单位不是 `1` , 而是 `sizeof(type)` . 例如:
```c
// pointer_pm.c
#include <stdio.h>
int main(){
    int a = 1 << 8;
    int *pi = &a;
    char *pc = &a;
    printf("pi:      %p\n", pi);
    printf("pi - 3:  %p\n", pi - 3);
    printf("--pi:    %p\n", --pi);
    printf("pc + 2:  %p\n", pc + 2);
    printf("++pc:    %p\n", ++pc);
    return 0;
}
```
*思考: 这段程序运行完后, `*pc` 的值是什么? 是随机值还是确定值?*

### 指针作为函数参数

由于函数接收的参数为**形式参数**, 只接收外部实参的值, 而参数名与外部参数没有关系, 所以通常来讲, 无法在函数内部修改外部参数的值, 如:
```c
// fake_swap.c
#include <stdio.h>
void swap(int a, int b){
    int tmp = a;
    a = b;
    b = tmp;
}
int main(){
    int a = 1, b = 2;
    swap(a, b);
    printf("a, b:  %d, %d\n", a, b);
    return 0;
}
```

而使用指针, 则可以实现修改外部参数的值的效果, 如:
```c
// swap.c
#include <stdio.h>
void swap(int *a, int *b){
    int tmp = *a;
    *a = *b;
    *b = tmp;
}
int main(){
    int a = 1, b = 2;
    swap(&a, &b);
    printf("a, b:  %d, %d\n", a, b);
    return 0;
}
```

需要注意的是, 不要把这个当成指针的特性, 而是要从原理层面理解, 对于 `a`, `b` 来说, 传入的同样是实参, 指针 `a`, `b` 的值 (即其指向的地址) 同样是不可修改的, 但如果在函数内修改了指向地址的值, 则在函数外时可以访问修改后的值的. 以下代码说明了在函数中修改指针本身的值同样是不会影响外部指针的值的:
```c
// pointer_change_func.c
#include <stdio.h>
void change(int *a){
    a++;
}
int main(){
    int *p = (int*)(0x04);
    printf("p (original): %p\n", p);
    change(p);
    printf("p (changed):  %p\n", p);
    return 0;
}
```
*思考: 如何使上述代码可以修改 `p` 的值?*

### 指针与数组

#### 指针与数组的对应关系

指针与数组有着对应关系:
- 数组名表示数组的地址, 即数组首元素的地址;
- 数组与指针的 `[]` 运算符与 `+` 运算符后取值是互通的.

如下所示:
```c
// array.c
#include <stdio.h>
int main(){
    int a[] = {1, 2, 3};
    int *p = &a[0];
    printf("a: %p\n", a);
    printf("p: %p\n", p);
    printf("*(a + 1): %d\n", *(a + 1));
    printf("p[1]:     %d\n", p[1]);
    printf("sizeof(a): %zu\n", sizeof(a));
    printf("sizeof(p): %zu\n", sizeof(p));
    return 0;
}
```
从上述例子中可以看出 `a` 与 `p` 基本可以转换使用, 但指针不包含数组的大小信息.

#### 动态数组

**静态数组**与**动态数组**的区别:
- 静态数组在**编译**时分配内存, **大小固定**; 动态数组在**运行**时手动分配内存, **大小可变**.
- 静态数组的内存通常分配在**栈**上, 随着函数的调用和返回而**自动管理**; 动态数组的内存空间在运行时通过动态内存分配函数**手动分配**, 并存储在**堆**上.
- 静态数组的生命周期始于其定义时, 自动终于在作用域中无法被访问时; 动态数组的生命周期由程序员控制, 需要在使用完数组后**手动释放内存**, 以避免**内存泄漏**.

可以使用 `<stdlib.h>` 库中的函数管理内存:
- `void* malloc(size_t size)` 函数: 分配所需的内存空间, 并返回一个指向它的指针.
- `void* calloc(size_t nitems, size_t size)` 函数: 分配所需的内存空间, 设置分配的内存为零, 并返回一个指向它的指针.
- `void* realloc(void *ptr, size_t size)` 函数: 重新分配内存空间.
- `void free(void *ptr)` 函数: 释放动态分配函数分配的内存.

当需要一个较大的内存空间时, 通常要使用动态数组, 因为栈的内存是有限的, 可能无法分配足够大的空间, 例如:
```c
// static.c
int main(){
    int N = 100000000;
    int a[N];
    return 0;
}
```
```c
// dynamic.c
#include <stdlib.h>
int main(){
    int N = 100000000;
    int *a = (int*)malloc(N * sizeof(int));
    if(!a) return 1;
    free(a);
    return 0;
}
```
其中的 `static.c` 代码就会出现 `Segmentation fault (core dumped)` .

以下给出一个二维动态数组的例子:
```c
// pointer2.c
#include <stdio.h>
#include <stdlib.h>
int main(){
    int m = 3, n = 5;
    int **arr = (int**)malloc(m * sizeof(int*));
    for(int i = 0; i < m; i++){
        arr[i] = (int*)calloc(n, sizeof(int));
    }
    for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            arr[i][j] = 10 * i + j;
        }
    }
    for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            printf("%02d ", *(arr[i] + j));
        }
        printf("\n");
    }
    for(int i = 0; i < m; i++){
        free(arr[i]);
    }
    free(arr);
    return 0;
}
```

使用 `malloc` 等函数分配完的内存, 一定要养成使用 `free` 释放的习惯! 如果不这样, 可能会出现内存泄露, 即申请的内存被占用但却无法正常访问, 也无法释放, 直到进程结束, 例如 (***谨慎运行***):
```c
// memory_leak.c
#include <stdlib.h>
#include <string.h>
#include <stdio.h>
void allocate(){
    char *p = malloc(100 * 1024 * 1024);
    memset(p, 0, 100 * 1024 * 1024);
}
int main(){
    for(int i = 0; i < 200; i++){
        allocate();
        printf("%d\n", i);
    }
    return 0;
}
```

#### 数组作为函数参数或返回值

数组作为函数参数, 实际上是把数组名隐式转化为指针传入函数, 即把数组的首地址传入, 而非把整个数组的值传入, 因此, 在函数内部是可以修改数组的, 例如:
```c
// array_change_func.c
#include <stdio.h>
void change(int a[]){
    printf("%zu\n", sizeof(a));
    a[1] = 4;
}
int main(){
    int a[] = {1, 2, 3};
    printf("%zu\n", sizeof(a));
    change(a);
    for(int i = 0; i < 3; i++){
        printf("%d ", a[i]);
    }
    printf("\n");
    return 0;
}
```

如果要传递多维数组, 需要把内部的维数写明, 而最外层的维数只做标识用, 如以下代码一般情况下可以正常运行:
```c
// array2_func.c
#include <stdio.h>
void change(int a[2][3]){
    a[2][2] = 1;
}
int main(){
    int a[3][3] = {0};
    change(a);
    printf("a[2][2]: %d\n", a[2][2]);
    return 0;
}
```

数组不能直接作为函数的返回值类型, 如果想要返回一个在函数中新定义的数组, 可以使用指针返回, 例如:
```c
// array_return.c
#include <stdio.h>
#include <stdlib.h>
int* new_array(int n){
    int *a = (int*)malloc(n * sizeof(int));
    for(int i = 0; i < n; i++){
        a[i] = i;
    }
    return a;
}
int main(){
    int n = 10;
    int *a = new_array(n);
    for(int i = 0; i < n; i++){
        printf("%d\n", a[i]);
    }
    free(a);
    return 0;
}
```

一个应用是用函数封装一个二维数组内存分配函数:
```c
// allocate.c
#include <stdio.h>
#include <stdlib.h>
int** alloc2(int m, int n){
    int **arr = (int**)malloc(m * sizeof(int*));
    for(int i = 0; i < m; i++){
        arr[i] = (int*)calloc(n, sizeof(int));
    }
    for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            arr[i][j] = 10 * i + j;
        }
    }
    return arr;
}
void free2(int** arr, int m){
    for(int i = 0; i < m; i++){
        free(arr[i]);
    }
    free(arr);
}
int main(){
    int m = 3, n = 5;
    int **arr = alloc2(m, n);
    for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            printf("%02d ", arr[i][j]);
        }
        printf("\n");
    }
    free2(arr, m);
    return 0;
}
```

值得注意的是, 如果在函数中只分配不返回, 函数结束后是无法访问的, 反而会造成内存泄露, 如:
```c
// wrong_allocate.c
#include <stdio.h>
#include <stdlib.h>
void alloc(int *a, int n){
    printf("a (original):   %p\n", a);
    a = (int*)malloc(n * sizeof(int));
    printf("a (allocated):  %p\n", a);
    for(int i = 0; i < n ; i++){
        a[i] = i;
    }
}
int main(){
    int n = 10;
    int *a = &n;
    alloc(a, n);
    printf("a (allocated?): %p\n", a);
    for(int i = 0; i < n; i++){
        printf("%d\n", a[i]);
    }
    free(a);
    return 0;
}
```

## 其它数据类型

### 结构体 `struct`

**结构体**是 C 语言中一种用户自定义的可用的数据类型, 它可以存储不同类型的数据项, 其数据成员可以是基本数据类型 (如 `int`, `float`, `char` 等), 也可以是其他结构体类型, 指针类型等.

可以使用 `.` 运算符访问结构体的成员变量, 也可以使用 `->` 运算符访问结构体指针的成员变量 ( `a->b` 等价于 `(*a).b` ), 例如:
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
struct Person{
    char name[32];
    unsigned age;
};
int main(){
    struct Person p1 = {"dmr", 70};
    struct Person *p2 = malloc(sizeof *p2);
    strcpy(p2->name, "ken");
    p2->age = 83;
    printf("%s, %d\n", p1.name, p1.age);
    printf("%s, %d\n", p2->name, p2->age);
    return 0;
}
```

结构体的赋值操作为**逐成员按值拷贝**, 这意味着每个所有成员变量的值会被复制一份, 而非两个结构体只是不同的名称, 但是指针成员变量会指向同一块内存. 例如:
```c
// struct_copy.c
#include <stdio.h>
struct Nums{
    int a;
    int *b;
};
int main(){
    int a = 1, b = 2;
    struct Nums n1 = {a, &b};
    struct Nums n2 = n1;
    n2.a = 3;
    *(n2.b) = 4;
    printf("n1: %d, %d\n", n1.a, *n1.b);
    printf("n2: %d, %d\n", n2.a, *n2.b);
    return 0;
}
```

### 共用体 `union`

**共用体**是一种特殊的数据类型, 可以在相同的内存位置存储不同的数据类型, 一个共用体可以有多个成员变量, 但任何时候只能有一个成员变量带有有效值. 共用体提供了一种使用相同的内存位置的有效方式, 例如:
```c
// union.c
#include <stdio.h>
union Nums{
    int a;
    short b;
}nums = {1 << 15};
int main(){
    printf("the size of Nums: %zu\n", sizeof(union Nums));
    printf("%d, %d\n", nums.a, nums.b);
    nums.a = 1 << 16;
    printf("%d, %d\n", nums.a, nums.b);
    return 0;
}
```

### 枚举 `enum`

**枚举**是 C 语言中的一种基本数据类型, 通常用于为程序中的一组相关的常量取名字, 以便于程序的可读性和维护性. 第一个枚举成员的默认值为整型的 `0` , 后续枚举成员的值在前一个成员上加 `1` , 以下只给出一个简单的例子:
```c
// enum.c
#include <stdio.h>
enum Weekdays{
    MON=1, TUE, WED, THU, FRI, SAT=11, SUN
};
int main(){
    for(int i = MON; i <= FRI; i++){
        printf("%d\n", i);
    }
    printf("%d\n%d\n", SAT, SUN);
    return 0;
}
```

### `typedef` 关键字

`typedef` 关键字可用来为类型取一个新的名字, 譬如在 `<types.h>` 库中, 就定义了 `__int8_t`, `__uint8_t`, `__int16_t`, `__uint16_t` 等类型, 分别对应于 `char`, `unsigned char`, `short`, `unsigned short` 等; 在 `<stddef.h>` 库中, 定义了 `size_t` 类型, 是用来表示对象大小, 内存大小, 元素个数的专用无符号类型.

`typedef` 的一个常用用途是给结构体取别名, 使得在使用时不需要再在前面加上 `struct` 关键字, 例如:
```c
// typedef.c
#include <stdio.h>
#include <stdlib.h>
typedef struct Vector{
    size_t n;
    int *data;
}Vector;
int main(){
    size_t n = 5;
    Vector a;
    a.n = n;
    a.data = (int*)malloc(n * sizeof(int));
    for(int i = 0; i < a.n; i++){
        a.data[i] = i;
    }
    for(size_t i = 0; i < a.n; i++){
        printf("%d\n", a.data[i]);
    }
    free(a.data);
    return 0;
}
```

## 补充知识

### `main` 函数参数

与其它函数类似, `main` 函数也可以有参数, 对于 `main.c` 编译好的程序 `main` , 可以通过
```bash
./main args1 args2 ...
```
运行, 在 `main` 函数中:
- `int argc` 表示参数的数目;
- `char **argv` 表示参数列表, 为字符串类型;

需要注意的是, 程序会默认把程序名作为第一个参数 (`argv[0]`), 对应上例即 `./main` .

以下给出一个可以接收并打印所有参数的代码示例:
```c
// main_args.c
#include <stdio.h>
int main(int argc, char **argv){
    printf("argc: %d\n", argc);
    for(int i = 0; i < argc; i++){
        printf("argv[%d] = %s\n", i, argv[i]);
    }
    return 0;
}
```

### 预处理器

**预处理器**是编译过程中的独立阶段, 在实际编译前对源代码进行文本处理, 主要功能包括:
- 宏展开;
- 头文件包含;
- 条件编译;
- 特殊指令处理.

可以用 `#define` 定义宏, 在编译过程中, 会直接进行文本替换; 可以用 `#undef` 删除对某个宏的定义 (需要注意不要随意 `#undef` 标准库中定义的宏, 否则很有可能发生编译错误). 比如:
```c
#define MY_INT_MAX 0x7fffffff
#undef MY_INT_MAX
```

可以用 `#include` 引入头文件.

可以用 `#if`, `#ifdef`, `#ifndef` 等来控制条件编译, 一个可能的应用场景是**头文件保护**, 即防止同一个头文件被多次包含, 甚至两个头文件互相包含导致错误的问题. 标准库中已经做好了头文件保护, 如果自己写的头文件, 可以按照如下格式做头文件保护:  
```c
#ifndef MY_HEADER_H
#define MY_HEADER_H

/* 头文件代码 */

#endif
```

在现代主流编译器, 对于头文件保护有更简单的写法:
```c
#pragma once
```

### 内存布局

一个运行中的 C 程序, 其内存通常可以分为五大区:
- 代码区
- 常量 / 只读数据区
- 全局 / 静态区
- 堆 (Heap)
- 栈 (Stack)

#### 栈

**栈**是一块连续的内存区域, 专门用于函数调用管理和局部数据存储, 其特点为:
- 先进后出;
- 自动分配和释放, 不需要手动管理;
- 内存有限制, 通常为 `MB` 量级.

在栈中存放的有:
- 局部变量;
- 函数参数;
- 函数返回地址与某些寄存器的状态.

每次函数调用都会在栈上创建一个**栈帧(Stack Frame)**, 也叫活动记录(Activation Record). 在大部分现代系统中, 栈帧**从高地址向低地址**增长. 函数调用结束时会弹出栈帧, 局部变量会自动销毁, 即生命周期结束.

栈有两个常见的问题: 一个为**栈溢出(Stack Overflow)**, 通常由递归太深或局部数组过大导致; 另一个为**野指针/悬空指针**, 即指向已释放内存区域的指针, 例如:
```c
// dangling_pointer.c
#include <stdio.h>
int* foo(){
    int x = 1;
    printf("&x: %p\n", &x);
    return &x;
}
int main(){
    int *p = foo();
    printf("p:  %p\n", p);
    printf("*p: %d\n", *p);
    return 0;
}
```

#### 堆

**堆**是程序运行时用于动态分配内存的一块区域, 其与栈相比的特点为:
- 变量的生命周期由程序员控制, 在程序结束前不会自动回收.
- 内存较大, 基本与系统的内存相当.
- 堆的地址一般比栈低, 栈和堆从不同方向增长, 即堆是**从低地址向高地址**增长.
- 堆的内存不一定连续, 可能会因多次 `malloc` 与 `free` 产生碎片化空间.
- 堆的内存分配比栈涉及更多流程, 故分配的速度比栈慢.

### 运行错误分析

以下只给出一些常见的**运行时报错**, 由于现代的 IDE 或编辑器插件都具有代码分析功能, 所以**编译时报错**的问题通常可被直接指出 (如下划红曲线).

根据具体环境不同, 有些未定义行为 (UB) 在某些平台会被认为是运行错误.

#### 算数错误

算术错误常发生于除以 `0` 的情形, 典型的报错信息为:
```bash
Floating point exception (core dumped)
```

#### 段错误

段错误一般发生于违规访问的情形, 如数组越界访问, 空指针解引用等, 典型的报错信息为:
```bash
Segmentation fault (core dumped)
```

栈溢出也会导致报错:
```bash
Segmentation fault (stack overflow)
```
