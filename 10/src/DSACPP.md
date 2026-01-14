# C++面向对象编程与数据结构简介

C++ 相对于 C , 增加了**类** `class` , 类是定义对象的模板, 与 C 中的结构体只能包含成员变量不同, 类包含有**成员变量**与**成员函数**.

以下为一个类的例子(仅做说明使用):

```cpp
class ClassName{
private:
    // 成员变量
    int capacity;
protected:
    int size;
    int *array;
public:
    // 构造函数
    ClassName(int c = 4)
        :capacity(c), size(0){
        array = new int[c];
    }
    // 析构函数
    ~ClassName(){
        delete [] array;
    }
};

class DerivedClass : public ClassName{
public:
    // 成员函数
    int sum(int n){
        if(n > size) n = size;
        int sum = 0;
        for(int i=0;i<n;i++) sum += array[i];
        return sum;
    }
};
```

其中:

* `public`, `private`, `protected` 是**访问修饰符**: `public` 表示公共成员, 在**类的内部与外部**均可访问; `private` 表示私有成员, 只能在**类的内部**访问; `protected` 表示受保护的成员, 在**类及其派生类**中可以被访问. 如果不作声明, 成员默认为私有.

* `ClassName()` 是**构造函数**, 在创建对象时**自动**调用, 用于初始化对象. `~ClassName()` 是**析构函数**, 在删除对象时**自动**调用, 用于清理资源. 这两个函数不需要设置返回类型, 命名需要与类的名称完全匹配, 构造函数可以重载, 析构函数不能传参.

* `DerivedClass` 是 `ClassName` 的派生类, 子类可以继承父类的属性与方法. `public` 表示子类从父类继承来的成员在子类中的访问级别保持不变; `private` 表示继承来的成员在子类中变为私有, 不能被子类的对象直接访问; `protected` 表示继承来的成员在子类中变为受保护的, 不能被子类的对象直接访问, 但可以在子类的派生类中被访问. 

C++ 还支持**模版类**, 可以创建与类型无关的类, 如:

```cpp
template <typename T> class TemplateClass{
    T data;
};
```

在声明变量时, 如令类型为 `int` , 可以按如下格式声明:

```cpp
TemplateClass<int> tc;
```

在 C++ 中, 结构体也有所改动, 除访问修饰符外, 其余功能与类基本相同, 以下对一些基本数据结构的介绍均不做封装, 以结构体形式呈现. 以下介绍均使用 C++ 代码(仅供参考, 如有问题请及时指出), 参考[邓俊辉老师的讲义](https://dsa.cs.tsinghua.edu.cn/~deng/ds/dsacpp/index.htm), 可以自行修改为 C 的形式使用; 如果要直接使用 C++ 的标准模版库(STL), 请自行查找资料.

## 向量(vector)

**向量**是**数组**的抽象与泛化, 由一组元素按**线性**次序封装而成, 各元素与 `[0,n)` 内的秩(`rank`)一一对应.

以下为一个包含基本的构造、操作与一些算法的 `Vector` 实现例子:

```cpp
#include <cstdlib>
#include <ctime>
using namespace std;
using Rank = unsigned int;
#define DEFAULT_CAPACITY 64 //默认容量

template <typename T> struct Vector{
// 成员变量
    Rank size;
    Rank capacity;
    T* elem;

// 辅助函数
    void CopyFrom(T const* A, Rank lo, Rank hi);
    void Expand(); //向量空间不足时扩容
    void Shrink(); //装填因子过小时压缩
    void Swap(Rank a, Rank b); //交换元素

// 构造函数
    Vector(Rank c = DEFAULT_CAPACITY); //默认构造
    Vector(Rank c, Rank s, T v); //容量为c,规模为s,所有元素初始为v;(s<=c)
    Vector(T const *A, Rank n); //数组整体复制
    Vector(T const *A, Rank lo, Rank hi); //数组区间复制
    Vector(Vector<T> const &V); //向量整体复制
    Vector(Vector<T> const &V, Rank lo, Rank hi); //向量区间复制

// 析构函数
    ~Vector(); //释放内部空间

// 插入删除函数
    Rank Insert(Rank r, T const &e); //插入元素//返回插入元素的位置
    Rank Insert(T const &e); //默认作为末元素插入//返回插入元素的位置
    Rank Remove(Rank lo, Rank hi); //删除秩在区间[lo, hi)之内的元素//返回被删元素的数目
    T Remove(Rank r); //删除秩为r的元素//返回被删元素

// 查找函数
    Rank BinSearch(T const &e, Rank lo, Rank hi); //有序向量二分查找算法

// 排序函数
    void BubbleSort(Rank lo, Rank hi); //起泡排序算法
    void Merge(Rank lo, Rank mi, Rank hi, T *former); //归并算法
    void MergeSort(Rank lo, Rank hi, T *former); //归并排序算法
    void MergeSort(Rank lo, Rank hi); //归并排序调用接口
    Rank Partition(Rank lo, Rank hi); //轴点构造算法
    void QuickSort(Rank lo, Rank hi); //快速排序算法
};

template <typename T> void Vector<T>::CopyFrom(T const *A, Rank lo, Rank hi){
    capacity = (hi-lo)*2;
    if(capacity < DEFAULT_CAPACITY) capacity = DEFAULT_CAPACITY; 
    elem = new T[capacity];
    for(size=0; lo<hi; size++,lo++) elem[size] = A[lo];
}
template <typename T> void Vector<T>::Expand(){ 
    if(size < capacity) return;
    T* oldelem = elem;
    CopyFrom(oldelem, 0 ,capacity);
    delete [] oldelem;
}
template <typename T> void Vector<T>::Shrink(){
    if(capacity < DEFAULT_CAPACITY<<1) return;
    if(size<<2 > capacity) return;
    T* oldelem = elem;
    capacity >>= 1;
    elem = new T[capacity];
    for(Rank i=0;i<size;i++) elem[i] = oldelem[i];
    delete [] oldelem;
}
template <typename T> void Vector<T>::Swap(Rank a, Rank b){
    T temp=elem[a];
    elem[a]=elem[b];
    elem[b]=temp;
}

template <typename T> Vector<T>::Vector(Rank c){
    size = 0;
    capacity = c;
    elem = new T[capacity];
}
template <typename T> Vector<T>::Vector(Rank c, Rank s, T v){
    capacity = c;
    elem = new T[capacity];
    for(size=0;size<s;size++) elem[size] = v;
}
template <typename T> Vector<T>::Vector(T const* A, Rank n){
    CopyFrom(A, 0, n);
}
template <typename T> Vector<T>::Vector(T const *A, Rank lo, Rank hi){
    CopyFrom(A, lo, hi);
}
template <typename T> Vector<T>::Vector(Vector<T> const &V){
    CopyFrom(V.elem, 0, V.size);
}
template <typename T> Vector<T>::Vector(Vector<T> const &V, Rank lo, Rank hi){
    CopyFrom(V.elem, lo, hi);
}

template <typename T> Vector<T>::~Vector(){
    delete [] elem;
}

template <typename T> Rank Vector<T>::Insert(Rank r, T const &e){
    Expand();
    for(Rank i=size;r<i;i--) elem[i] = elem[i-1];
    elem[r] = e;
    size++;
    return r;
}
template <typename T> Rank Vector<T>::Insert(T const &e){
    return Insert(size, e);
}
template <typename T> Rank Vector<T>::Remove(Rank lo, Rank hi){
    if(lo == hi) return 0;
    for(; hi<size; lo++,hi++) elem[lo] = elem[hi];
    size = lo;
    Shrink(); 
    return hi-lo;
}
template <typename T> T Vector<T>::Remove(Rank r){
    T e = elem[r];
    Remove(r, r+1);
    return e;
}

template <typename T> Rank Vector<T>::BinSearch(T const &e, Rank lo, Rank hi){
    while(lo < hi){
        Rank mi = (lo+hi)>>1;
        if(e < elem[mi]) hi = mi;
        else lo = mi+1;
    }
    return lo-1;
}

template <typename T> void Vector<T>::BubbleSort(Rank lo, Rank hi){
    for(Rank last; lo<hi; hi=last){
        last = lo;
        for(Rank i=lo+1; i<hi; i++){
            if(elem[i-1] > elem[i]){
                Swap(i-1, i);
                last = i;
            }
        }
    } 
}
template <typename T> void Vector<T>::Merge(Rank lo, Rank mi, Rank hi, T *former){
    Rank lf = mi-lo, ll = hi-mi;
    T *all = elem+lo, *latter = elem+mi;
    for (Rank i=0;i<lf;i++) former[i] = all[i];
    Rank i=0,j=0,k=0;
    while(j<lf && k<ll){
        if(former[j] <= latter[k]){
            all[i] = former[j];
            i++;
            j++;
        }
        else{
            all[i] = latter[k];
            i++;
            k++;
        }
    }
    while(j<lf){
        all[i] = former[j];
        i++;
        j++;
    }
}
template <typename T> void Vector<T>::MergeSort(Rank lo, Rank hi, T *former){
    if(hi-lo < 2) return;
    Rank mi = (lo+hi)>>1;
    MergeSort(lo, mi, former);
    MergeSort(mi, hi, former);
    Merge(lo, mi, hi, former);
}
template <typename T> void Vector<T>::MergeSort(Rank lo, Rank hi){
    T* former = new T[((hi-lo)>>1) + 1];
    MergeSort(lo, hi, former);
    delete [] former;
}
template <typename T> Rank Vector<T>::Partition(Rank lo, Rank hi){
    srand(time(0));
    Swap(lo, lo+rand()%(hi-lo));
    T pivot = elem[lo];
    while(lo<hi){
        do hi--;
        while((lo<hi) && (pivot<elem[hi]));
        if(lo<hi) elem[lo] = elem[hi];
        do lo++;
        while((lo<hi) && (elem[lo]<pivot));
        if(lo<hi) elem[hi] = elem[lo];
    }
    elem[hi] = pivot;
    return hi;
}
template <typename T> void Vector<T>::QuickSort(Rank lo, Rank hi){
    if(hi-lo < 2) return;
    Rank mi = Partition(lo, hi);
    QuickSort(lo, mi);
    QuickSort(mi+1, hi);
}
```

## 列表(list)

#### 静态与动态的操作与存储

根据是否修改数据结构, 所有操作大致分为两类方式:

* **静态**: 仅**读取**, 数据结构的内容与组成一般不变 (如`search`);

* **动态**: 需**写入**, 数据结构的局部或整体将改变 (如`sort`).

与操作方式相对应地, 数据元素的存储与组织方式也分为两种:

* **静态**: 数据空间**整体创建或销毁**. 数据元素的物理次序与其逻辑次序严格一致; 可支持高效的静态操作. (比如向量, 元素的物理地址与其逻辑次序线性对应)

* **动态**: 为**各数据元素动态地分配和回收的物理空间**. 相邻元素记录彼此的物理地址, 在逻辑上形成一个整体; 可支持高效的动态操作.

#### 列表介绍

列表是采用**动态**储存策略的典型结构, 其中的元素称作**节点**(`node`), 通过**指针或引用**彼此联接, 在**逻辑**上构成一个**线性**序列.

列表的相邻节点彼此互称**前驱**(predecessor)或**后继**(successor), 没有前驱/后继的节点称作**首**(first)/**末**(last)节点. 由此形成下列双向列表结构:(一个[]代表一个节点)

[|first|s] <---> [p|B|s] <---> [p|C|s] <---> ... <---> [p|Y|s] <---> [p|last|] 

如此, 列表中各元素的物理地址将不再决定于逻辑次序, 不同于向量的**循秩访问** (call by rank), 而是采用**循位置访问** (call by position): 利用节点之间的相互引用, 找到特定的节点.

#### 列表代码

首先给出列表节点的代码 `ListNode.h`, 每个节点包含有 3 个成员变量: 数值、前驱、后继.

```cpp
#include <iostream>
using Rank = unsigned int;

template <typename T> struct ListNode;
template <typename T> using ListNodePosi = ListNode<T>*; //列表节点位置

template <typename T> struct ListNode{ //列表节点模板类(以双向链表形式实现)
// 成员
    T data; //数值
    ListNodePosi<T> pred, succ; //前驱、后继

// 构造函数
    ListNode(){} //针对head和tail的构造
    ListNode(T const &e, ListNodePosi<T> p = NULL, ListNodePosi<T> s = NULL)
        :data(e), pred(p), succ(s){} //默认构造器(类T须定义复制方法)

// 操作接口
    ListNodePosi<T> InsertPred(T const &e){
        ListNodePosi<T> x = new ListNode(e, pred, this);
        pred->succ = x;
        pred = x;
        return x;
    } //紧靠当前节点之前插入新节点
    ListNodePosi<T> InsertSucc(T const &e){
        ListNodePosi<T> x = new ListNode(e, this, succ);
        succ->pred = x;
        succ = x;
        return x;
    } //紧随当前节点之后插入新节点
};
```

为了便于列表的构造与操作, 在首节点之前引入哨兵**头节点**(`head`), 在末节点之后引入哨兵**尾结点**(`tail`), 这样所有列表内部的节点都拥有了前驱与后继. 此时, 对于有`n`个内部节点的列表, 可以将头、首、末、尾节点的秩视为 `-1`、`0`、`n-1`、`n` .

以下为一个包含基本的构造与操作的 `List` 实现例子:

```cpp
#include "ListNode.h"

template <typename T> struct List{ //列表模板类
// 成员变量
    Rank size; //规模
    ListNodePosi<T> head, tail; //头哨兵、尾哨兵

// 辅助函数
    void Init(); //列表创建时的初始化
    void CopyNodes(ListNodePosi<T> p, Rank n); //复制列表中自位置p起的n项
    Rank Clear(); //清除所有节点

// 构造函数
    List(); //默认
    List(List<T> const &L); //整体复制列表L
    List(List<T> const &L, Rank r, Rank n); //复制列表L中自第r项起的n项
    List(ListNodePosi<T> p, Rank n); //复制列表中自位置p起的n项

// 析构函数
    ~List(); //释放(包含头、尾哨兵在内的)所有节点

// 运算符重载
    ListNodePosi<T> operator[](Rank r) const; //重载,支持循秩访问（效率低）

// 插入删除函数
    ListNodePosi<T> InsertFirst(T const &e); //将e当作首节点插入
    ListNodePosi<T> InsertLast(T const &e); //将e当作末节点插入
    ListNodePosi<T> Insert(ListNodePosi<T> p, T const &e); //将e当作p的后继插入
    ListNodePosi<T> Insert(T const &e, ListNodePosi<T> p); //将e当作p的前驱插入
    T Remove(ListNodePosi<T> p); //删除合法位置p处的节点,返回被删除节点

// 遍历
    void Traverse(void (*visit)(T&)); //依次实施visit操作（函数指针）
    template <typename VST> void Traverse(VST &visit); //依次实施visit操作（函数对象）
};

template <typename T> void List<T>::Init(){
    head = new ListNode<T>;
    tail = new ListNode<T>;
    head->succ = tail;
    head->pred = NULL;
    tail->pred = head;
    tail->succ = NULL;
    size = 0;
}
template <typename T> void List<T>::CopyNodes(ListNodePosi<T> p, Rank n){ 
    //请确保p合法，且至少有n-1个真后继
    Init();
    for(; n>0; n--){
        InsertLast(p->data);
        p = p->succ;
    }
}
template <typename T> Rank List<T>::Clear(){
   Rank oldsize = size;
   while(size > 0) Remove(head->succ);
   return oldsize;
}

template <typename T> List<T>::List(){
    Init();
}
template <typename T> List<T>::List(List<T> const &L){
    CopyNodes(L.head->succ, L.size);
}
template <typename T> List<T>::List(List<T> const &L, Rank r, Rank n){
    //请确保 r+n <= L.size
    ListNodePosi<T> p = L.head->succ;
    for(; r>0; r--) p = p->succ;
    CopyNodes(p, n);
}
template <typename T> List<T>::List(ListNodePosi<T> p, Rank n){
    CopyNodes(p, n);
}

template <typename T> List<T>::~List(){
    Clear();
    delete head;
    delete tail;
}

template <typename T> ListNodePosi<T> List<T>::operator[](Rank r) const{
    //请确保 0 <= r < size
    ListNodePosi<T> p = head->succ;
    for(; r>0; r--) p = p->succ;
    return p;
}

template <typename T> ListNodePosi<T> List<T>::InsertFirst(T const &e){
    size++;
    return head->InsertSucc(e);
}
template <typename T> ListNodePosi<T> List<T>::InsertLast(T const &e){
    size++;
    return tail->InsertPred(e);
}
template <typename T> ListNodePosi<T> List<T>::Insert(ListNodePosi<T> p, T const &e){
    size++;
    return p->InsertSucc(e);
}
template <typename T> ListNodePosi<T> List<T>::Insert(T const &e, ListNodePosi<T> p){
    size++;
    return p->InsertPred(e);
}
template <typename T> T List<T>::Remove(ListNodePosi<T> p){
    //请确保p合法
    T e = p->data;
    p->pred->succ = p->succ;
    p->succ->pred = p->pred;
    delete p;
    size--;
    return e;
}

template <typename T> void List<T>::Traverse(void (*visit)(T&)){
    for(ListNodePosi<T> p=head->succ; p!=tail; p=p->succ) visit(p->data);
}
template <typename T> template <typename VST> void List<T>::Traverse(VST &visit){
    for(ListNodePosi<T> p=head->succ; p!=tail; p=p->succ) visit(p->data);
}
```

## 栈与队列

#### 栈(stack)

**栈**是受限的线性序列:

- 只能在**栈顶(top)插入和删除**;

- 栈底(bottom)为盲端.

其包含 3 个基本接口:

* **入栈** `Push()`: 将元素插入栈顶;

* **出栈** `Pop()`: 将栈顶元素取出;

* **查顶** `Top()`: 查询栈顶元素.

栈的实现不难, 以下分别给出根据向量与列表派生得到的栈的例子:

```cpp
#include "Vector.h"

template <typename T> struct Stack : Vector<T>{
    //入栈: 等效于将新元素作为向量的末元素插入
    void Push(T const& e){
        Vector<T>::Insert(e);
    }
    //出栈: 等效于删除向量的末元素
    T Pop(){
        return Vector<T>::Remove(Vector<T>::size - 1);
    }
    //查顶: 直接返回向量的末元素
    T& Top(){
        return Vector<T>::elem[Vector<T>::size - 1];
    }
};
```

```cpp
#include "List.h"

template <typename T> struct Stack : List<T>{
    //入栈: 等效于将新元素作为列表的末元素插入
    void Push(T const& e){
        List<T>::InsertLast(e);
    }
    //出栈: 等效于删除列表的末元素
    T Pop(){
        return List<T>::Remove(List<T>::tail->pred);
    }
    //查顶: 直接返回列表的末元素
    T& Top(){
        return List<T>::tail->pred->data;
    } 
};
```

#### 队列(queue)

**队列**与栈类似, 也是受限的线性序列:

* 只能在**队尾插入**(查询): `Enqueue()` / `Rear()`

* 只能在**队首删除**(查询): `Dequeue()` / `Front()`

队列的实现与栈类似, 以下给出根据列表派生得到的队列的例子:

```cpp
#include "List.h"

template <typename T> struct Queue : List<T>{
    //入队: 尾部插入
    void Enqueue(T const& e){
        List<T>::InsertLast(e);
    }
    //出队: 首部删除
    T Dequeue(){
        return List<T>::Remove(List<T>::head->succ);
    }
    //队首
    T& Front(){
        return List<T>::head->succ->data;
    }
};
```

## 树(tree)

#### 树介绍

**树**是一种半线性、具有层次的数据结构, 由一系列相联的节点组成, 每个节点除了存储本身的信息外, 还有着指向其**父节点**(`parent`)与**子节点**(`child`)的**指针**. 除**根节点**(`root`)外, 每个节点拥有一个**父亲**, 所有节点都拥有非负个**孩子**(没有孩子的节点叫做**叶节点**), 一个节点的孩子互为**兄弟**, 从根节点向下延伸成一个近似于三角形的树状. 将一个节点及其所有后代取出, 即为以该节点为根的一颗**子树**, 其中的节点数叫做该子树的**规模**.

注: 这里不介绍与**平衡树**有关的概念, 也不做时间复杂度的分析, 故略去**高度**与**深度**的概念, 可自行了解.

#### 二叉树(binary tree)

**二叉树**是指所有节点的孩子数目不超过 2 , 此时可以用左右来区分两个孩子, 即**左孩子**(`lc`)、**右孩子**(`rc`). 引入**外部节点**(即空节点`NULL`)后, 可以使得原有节点的孩子数目都为 2 , 便于理解与分析.

首先给出二叉数节点模版类的代码 `BinNode.h` :

```cpp
#include <iostream>
template <typename T> struct BinNode;
template <typename T> using BinNodePosi = BinNode<T>*; //节点位置
using Rank = unsigned int;

template <typename T> struct BinNode{ //二叉树节点模板类
// 成员变量
   T data; //数值
   BinNodePosi<T> parent, lc, rc; //父节点及左右孩子

// 构造函数
    BinNode()
        :parent(NULL), lc(NULL), rc(NULL){}
    BinNode(T e, BinNodePosi<T> p = NULL, BinNodePosi<T> l = NULL, BinNodePosi<T> r = NULL)
        :data(e), parent(p), lc(l), rc(r){
            if(lc) lc->parent = this;
            if(rc) rc->parent = this;
        }

// 操作接口
    BinNodePosi<T> InsertLc(T const& e){ //插入左孩子
        return lc = new BinNode<T>(e, this);
    }
    BinNodePosi<T> InsertRc(T const& e){ //插入右孩子
        return rc = new BinNode<T>(e, this);
    }
    void AttachLc(BinNodePosi<T> x){ //接入左子树
        lc = x;
        if(x) x->parent = this;
    }
    void AttachRc(BinNodePosi<T> x){ //接入右子树
        rc = x;
        if(x) x->parent = this;
    }
    bool IsLeftChild(){ //判断当前节点是否为左孩子
        if(parent){
            if(this == parent->lc) return true;
        }
        return false;
    }
    bool IsRightChild(){ //判断当前节点是否为右孩子
        if(parent){
            if(this == parent->rc) return true;
        }
        return false;
    }
    BinNodePosi<T> Succ(){ //取当前节点的直接后继
        BinNodePosi<T> s = this;
        if(rc){
            s = rc;
            while(s->lc) s = s->lc;
        }
        else{
            while(s->IsRightChild()) s = s->parent;
            s = s->parent;
        }
        return s;
    }
};
```

以下为一个包含基本的构造与操作的 `BinTree` 实现例子:

```cpp
#include "BinNode.h" //引入二叉树节点类

template <typename T> struct BinTree { //二叉树模板类
// 成员变量
    BinNodePosi<T> root; //根节点

// 构造函数
    BinTree() :root(NULL){} //默认构造方法
    BinTree(BinTree<T> const& s); //复制方法

// 析构函数
    ~BinTree();

// 插入删除函数
    BinNodePosi<T> Insert(T const& e); //插入根节点
    BinNodePosi<T> Insert(T const& e, BinNodePosi<T> x); //插入左孩子
    BinNodePosi<T> Insert(BinNodePosi<T> x, T const& e); //插入右孩子
    BinNodePosi<T> Attach(BinTree<T> S, BinNodePosi<T> x); //接入左子树
    BinNodePosi<T> Attach(BinNodePosi<T> x, BinTree<T> S); //接入右子树
    Rank Remove(BinNodePosi<T> x); //子树删除
    BinTree<T>* Secede(BinNodePosi<T> x); //子树分离
};

// 将以s为根的子树的节点复制,得到的子树的根以p为父亲
template <typename T> BinNodePosi<T> NodeCopy(BinNodePosi<T> p, BinNodePosi<T> s){
    if(!s) return NULL;
    BinNodePosi<T> t = new BinNode<T>(s->data, p, NULL, NULL);
    t->lc = NodeCopy(t, s->lc);
    t->rc = NodeCopy(t, s->rc);
    return t;
}
template <typename T> BinTree<T>::BinTree(BinTree<T> const& s){
    root = NodeCopy<T>(NULL, s.root);
}

template <typename T> BinTree<T>::~BinTree(){
    if(!root) Remove(root);
}

template <typename T> BinNodePosi<T> BinTree<T>::Insert(T const& e){
    return root = new BinNode<T>(e);
}
template <typename T> BinNodePosi<T> BinTree<T>::Insert(T const& e, BinNodePosi<T> x){
    x->InsertLc(e);
    return x->lc;
}
template <typename T> BinNodePosi<T> BinTree<T>::Insert(BinNodePosi<T> x, T const& e){
    x->insertRc(e);
    return x->rc;
}
template <typename T> BinNodePosi<T> BinTree<T>::Attach(BinTree<T> S, BinNodePosi<T> x){
    //请确保 x->lc == NULL
    if(x->lc = S.root) x->lc->parent = x;
    S.root = NULL; //释放原树
    return x; 
}
template <typename T> BinNodePosi<T> BinTree<T>::Attach(BinNodePosi<T> x, BinTree<T> S){
    //请确保 x->rc == NULL
    if(x->rc = S.root) x->rc->parent = x;
    S.root = NULL; //释放原树
    return x;
}
//删除二叉树中位置x处的节点及其后代，返回被删除子树的规模
template <typename T> static Rank RemoveAt(BinNodePosi<T> x){
    //请确保 x为二叉树中的合法位置
    if(!x) return 0;
    Rank n = 1 + RemoveAt(x->lc) + RemoveAt(x->rc);
    delete x;
    return n;
}
template <typename T> Rank BinTree<T>::Remove(BinNodePosi<T> x){
    //请确保 x为二叉树中的合法位置
    if(x->IsLeftChild()) x->parent->lc = NULL;
    else if(x->IsRightChild()) x->parent->rc = NULL;
    else root = NULL;
    Rank n = RemoveAt( x );
    return n;
}
template <typename T> BinTree<T>* BinTree<T>::Secede(BinNodePosi<T> x){
    //请确保 x为二叉树中的合法位置
    if(x->IsLeftChild()) x->parent->lc = NULL;
    else if(x->IsRightChild()) x->parent->rc = NULL;
    else root = NULL;
    BinTree<T>* S = new BinTree<T>;
    S->root = x;
    x->parent = NULL;
    return S;
}
```

#### 二叉搜索树(binary search tree)

在二叉树中, 如果规定任一节点的数值均不小于其左后代, 均不大于其右后代 (等效于均不小于其左孩子, 均不大于其右孩子; 也可以做相反的规定), 此时的二叉树便具有了顺序性, 叫做**二叉搜索树**. 为了简化, 假定所有节点的数值都不相等.

二叉搜索树可由二叉树派生得到, 增加了对于数值进行查找、插入、删除的 3 个函数. 二叉搜索树的查找 `Search()` 与向量的二分查找原理类似, 从根节点开始, 在每个节点处判断是否命中, 若未命中则根据相对大小深入左/右子树继续查找; 为了插入与删除操作在查找操作的基础上正确进行, 在每次调用查找函数 `Search()` 后, 用 `hot` 记录最终**命中节点的父亲**(特别的, 若命中根节点, 则 `hot = NULL` , 若未命中节点, 则 `hot` 为最后一次到达的有效节点). 二叉搜索树的插入 `Insert()` 和删除 `Remove()` 大体上与列表相同, 特别的是, 当删除的节点有 2 个真孩子时, 无法直接重建链接关系, 需要找到该节点的**直接后继**(即所有数值大于该节点数值的节点中最小的那一个), 其必然只有 1 个真孩子, 可将其与原待删除节点交换后删除. 

以下为一个包含基本操作的 `BST` 实现例子:

```cpp
#include "BinTree.h"

template <typename T> struct BST : BinTree<T>{ //二叉搜索树模板类
// "命中"节点的父亲
    BinNodePosi<T> hot;
// 查找
    BinNodePosi<T>& Search(const T& e){
        if(!BinTree<T>::root || e==BinTree<T>::root->data){
            hot = NULL;
            return BinTree<T>::root;
        }
        BinNodePosi<T>& v = BinTree<T>::root;
        for(hot=BinTree<T>::root; ; ){
            if(e < hot->data) v = hot->lc;
            else v = hot->rc;
            if(!v || e==v->data) return v;
            hot = v;
        }
    }
// 插入
    BinNodePosi<T> Insert(const T& e){
        BinNodePosi<T>& x = Search(e);
        if(x) return x; //节点已存在,无需重复插入
        x = new BinNode<T>(e, hot);
        return x;
    } //无论e是否存在于原树中, 返回时总有 x->data == e
// 删除
    bool Remove(const T& e){
        BinNodePosi<T>& x = Search(e);
        if(!x) return false; //节点不存在,不能删除
        BinNodePosi<T> w = x; //实际被删除节点
        BinNodePosi<T> succ = NULL; //实际被删除节点的接替者
        if( !x->lc ){
            x = x->rc;
            succ = x;
        }
        else if( !x->rc ){
            x = x->lc;
            succ = x;
        }
        else{ //若左右子树均存在,则选择x的直接后继作为实际被摘除节点
            w = w->Succ();
            x->data = w->data;
            succ = w->rc;
            if(w->IsRightChild()) w->parent->rc = succ;
            else w->parent->lc = succ;
        }
        hot = w->parent;
        if(succ) succ->parent = hot;
        delete w;
        return true;
    }
};
```

#### 多叉树

若节点的孩子数目可以大于 2 , 则这棵树为**多叉树**. 多叉树可直接通过将节点的孩子指针存入一个**列表**来实现, 若已知每个节点孩子数目上限(不是很大时, 如三叉树), 也可存入**数组**中; 多叉树也可以使用**长子-兄弟表示法**转化为二叉树: 对于一个节点, 把它的**长子**记为其**左孩子**, 将**最大的弟弟**记为其**右孩子**(这样, 次子就是长子的右孩子, 以此类推).

## 散列表(hash table)

**散列**是一种**循对象访问**的数据结构, 一个散列由若干桶组成, 每个桶中可存放一个**词条**(`Entry`), 每个词条包含有**关键码**(`key`)与**数值**(`value`)两个成员变量(也叫**键值对**). 

以下为词条模版类的一个实现方式:

```cpp
template <typename K, typename V> struct Entry{ //词条模板类
    K key; //关键码
    V value; //数值
    Entry(K k = K(), V v = V()) :key(k), value(v){}; //默认构造方法
    Entry(Entry<K, V> const& e) :key(e.key), value(e.value){}; //基于克隆的构造方法
    // 可通过重载运算符自行补充判等器与比较器
};
```

通过散列表, 可以在 O(1) 的时间由关键码访问数值, 这需要将词条的关键码通过**散列函数** `hash()` 映射到 `[0,M)` 这一区间的整数中, 其中 `M` 为桶总数. 当数据集已知且固定时, 理论上可以实现 `n` 个词条从任意互异关键码映射到 `[0,n)` 的**双射**散列函数, 从而构造**完美散列**; 但对于未知的或动态变化的数据集, 无法实现完美散列. 记 `λ = N/M` 为**装填因子**, `N` 为词条数, `λ` 越大, **空间利用率**越高, 但出现**冲突**(不同关键码具有相同的 `hash` 值)的情况会越严重,  故为了实现正确查找, 同时保证较高的空间利用率, 需要完成以下 2 个基本任务:

* 精心设计散列表及散列函数 `hash()` , 尽可能降低冲突的概率;

* 制定可行的预案, 以便在发生冲突时, 能够尽快予以排解.

对于散列函数的设计, 常用的方法为**除余法**, 即 `hash(key) = key % M`, 且桶总数 `M` 应取为素数; 更进一步地, 可以使用**MAD法**(Multiply-Add-Divide), 即 `hash(key) = (a * key + b) % M`, 其中 `M` 为素数, `a > 1`, `b > 0`, `a % M != 0`. 特别的, 如果词条的 `value` 均为字符串, 设字符集中字符总数为 `S`, 考虑将字符串转化为 `S` 进制的整数作为 `key`, 但碍于存储限制, `key` 的值可能超出 `int` 甚至 `long long int` 的范围, 可以考虑用 `key` 计算 `hash` 值后(注:  $(S\times A+B)\mod M=(S\times(A\mod M)+B)\mod M$ ) , 再通过进一步比较字符串的内容是否相同来判定词条是否相同, 而真实的 `key` 值无需记录. 

对于冲突的排解, 有**开放散列**与**封闭散列**两种解决策略. **开放散列**每个桶中可容纳词条数不限, 每个桶中可进入的词条类型有限制, 其实现原理较为简单: 每个桶拥有一个**列表**, 存放对应的一组 `hash` 值相同的词条. **封闭散列**每个桶中只可容纳 1 个词条, 但每个桶中可进入的词条类型没有限制, 其实现原理相对复杂: 对于每一组 `hash` 值相同的词条, 都事先约定了若干备用桶, 依次串接成**试探链**, 存放时沿试探链查找到第一个**空桶**时放入, 读取时沿试探链查找词条, 至第一个**未曾存放过词条的桶**时查找失败. 对于**试探链**的选取, 可以采用最简单的**线性试探**: 试探的桶序号每次 +1, 即 $r_i(key)=(\text{hash}(key)+i)\mod M,\kern6pt i=0,1,2,3,\dots$ , 这种试探在桶存满时需**扩容**, 为了保证试探次数的期望 $E<2$ , 建议装填因子 $\lambda<0.5$ ; 为了避免出现连续的失败试探, 可以采用**平方试探**:  $r_i(key)=(\text{hash}(key)+i^2)\mod M,\kern6pt i=0,1,2,3,\dots$ , 这种试探必须保证装填因子 $\lambda<0.5$, 避免出现有空桶但找不到的情况; 进一步的, 为了增大空间利用率, 可以采用**双向平方试探**: $r_i(key)=(\text{hash}(key)+(-1)^{i+1}\cdot (\left\lceil i/2\right\rceil)^2)\mod M,\kern6pt i=0,1,2,3,\dots$ , 这种试探的素数 $M$ 需要保证 $M=4\cdot k+3$ , 此时必然能保证所有桶都被试探链前 $M$ 项包含.

以下为一个包含基本的构造、操作的 `HashTable` 实现例子:

```cpp
#include "Entry.h"
#include <iostream>
using Rank = unsigned int;
#define DEFAULT_CAPACITY 7

// 查找不小于l的最小的4k+3素数
Rank Prime(Rank l){
    if(l > 0x7FFFFFFF) return l; //初始时l过大,直接返回l//一般不应出现
    Rank p = l;
    p += 3 - (p%4);
    bool isprime = 1;
    for(Rank h=1; ; p+=4){
        isprime = 1;
        while(h*h < p) h++;
        for(Rank i=3; i<h; i++){
            if(p%i == 0){
                isprime = 0;
                break;
            }
        }
        if(isprime) return p;
    }
}

template <typename K, typename V> struct HashTable{
// 成员变量
    Entry<K, V>** ht; //桶数组,存放词条指针
    bool* removed; //懒惰删除标记(可改为使用位图)//C++布尔数组内存管理有时会出现问题,可改为int
    Rank M, N, S; //桶的总数、词条的数目、使用过的桶数

// 散列函数(请自行选择hash函数,这里给出一个示例)
    Rank hash(K k){return ((Rank)k)%M;}

// 构造函数
    HashTable(Rank c = DEFAULT_CAPACITY); //创建一个容量不小于c的散列表

// 析构函数
    ~HashTable(); //释放桶数组及其中各(非空)元素所指向的词条

// 试探函数
    Rank Probe4Hit(const K& k); //沿关键码k对应的试探链,找到词条匹配的桶
    Rank Probe4Free(const K& k); //沿关键码k对应的试探链,找到首个可用空桶

// 扩容/减容函数
    void Rehash(); //重散列算法: 扩充桶数组,保证装填因子在警戒线以下

// 插入删除读取函数
    bool Put(K k, V v); //插入(禁止词条相等,故可能失败)
    bool Remove(K k); //删除
    V* Get(K k); //读取
};

template <typename K, typename V> HashTable<K,V>::HashTable(Rank c){
    M = Prime(c); 
    N = 0;
    S = 0;
    ht = new Entry<K, V>*[M];
    for(Rank i=0;i<M;i++) ht[i] = NULL;
    removed = new bool[M];
    for(Rank i=0;i<M;i++) removed[i] = false;
}

template <typename K, typename V> HashTable<K,V>::~HashTable(){
    for(Rank i=0;i<M;i++){
        if(ht[i]) delete ht[i];
    }
    delete [] ht;
    delete [] removed;
}

template <typename K, typename V> Rank HashTable<K, V>::Probe4Hit(const K& k){
    //采用双向平方试探
    for(int r=k,i=2; ; i++){
        if((ht[r] && k==ht[r]->key) || (!ht[r] && !removed[r])) return r;
        if(i%2==0) r = k + (i>>1)*(i>>1);
        else r = k - (i>>1)*(i>>1);
        while(r < 0) r += M;
        r %= M;
    }
}
template <typename K, typename V> Rank HashTable<K, V>::Probe4Free(const K& k){
    //采用双向平方试探
    for(int r=k,i=2; ; i++){
        if(!ht[r]) return r;
        if(i%2==0) r = k + (i>>1)*(i>>1);
        else r = k - (i>>1)*(i>>1);
        while(r < 0) r += M;
        r %= M;
    }
}

template <typename K, typename V> void HashTable<K, V>::Rehash(){
    Rank oldM = M;
    Entry<K, V>** oldht = ht;
    M = Prime(4*N);
    N = 0;
    S = 0;
    ht = new Entry<K, V>*[M];
    for(Rank i=0;i<M;i++) ht[i] = NULL;
    delete removed;
    removed = new bool[M];
    for(Rank i=0;i<M;i++) removed[i] = false;
    for(Rank i=0;i<oldM;i++){
        if(oldht[i]) Put(oldht[i]->key, oldht[i]->value);
    }
    delete [] oldht;
}

template <typename K, typename V> bool HashTable<K, V>::Put(K k, V v){
    if( ht[Probe4Hit(k)] ) return false; //忽略相等元素
    Rank r = Probe4Free(k); 
    ht[r] = new Entry<K, V>(k, v);
    N++;
    if(removed[r]) removed[r] = false;
    else S++;
    if(2*S > M) Rehash(); //若装填因子高于50%,重散列
    return true;
}
template <typename K, typename V> bool HashTable<K, V>::Remove(K k){
    Rank r = Probe4Hit(k);
    if( !ht[r] ) return false; //确认目标词条确实存在
    delete ht[r];
    ht[r] = NULL;
    N--;
    removed[r] = true;
    return true;
}
template <typename K, typename V> V* HashTable<K, V>::Get(K k){
    Rank r = Probe4Hit(k);
    if(ht[r]) return &(ht[r]->value);
    else return NULL;
}
```
