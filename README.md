# CS-EASY-03 内存模型
## 简答题
### C语言内存模型
|内存分区	|具体含义 |应用场景 |调用方式 |
| :--- | :---: | :---: | :---: |
|程序代码区 (code)|存储程序可执行代码的区域	|存放编译后的机器指令，程序执行时从此区域读取指令|由操作系统自动加载和执行
|常量区 (constant)|存储常量字符串、常量数值等|存储在程序运行过程中不会改变的值，如字符串常量、字面常量等|直接引用，例如在代码中使用字符串常量时会自动从常量区读取
|全局数据区 (global data)|存储全局变量和静态变量|用于需要在多个函数或文件之间共享数据的情况，以及需要在整个程序生命周期内保持存在的数据|可以在程序的任何地方通过变量名直接访问全局变量，静态变量根据其作用域在特定的函数或文件内访问
|堆区 (heap)|存储使用malloc等函数动态分配的内存|当需要在运行时动态确定内存大小或者需要分配较大内存块时使用|	通过调用 malloc、realloc 等函数进行分配，使用 free 函数释放
|动态链接库|一种可在运行时被多个程序共享的二进制文件|提供一些通用的功能函数，减少程序的重复开发，提高代码复用性|在程序中通过链接动态库，并使用相应的函数调用约定进行调用	
|栈区 (stack)|存储局部变量和函数参数的地方|存放函数执行过程中的临时数据，如局部变量、函数调用时的参数传递等|由编译器自动管理，函数调用时自动分配空间，函数返回时自动释放空间

尝试回答下列导学问题：

1.什么是“栈溢出”？
答：栈区空间被耗尽的一种错误情况

2.堆区和栈区的区别是什么？
答：堆区的内存需要手动进行申请栈区的内存只需定义局部变量就可申请，栈区运行效率快与堆区，栈区内存小堆区内存很大，栈区存储方式是连续的（后进先出）堆区存储是不连续的，栈区的变量生命周期只在函数中存在堆区的生命周期可以手动控制

3.程序运行过程中，内存模型当中的哪些区是只读的，哪些区是可读写的？
答：只读有代码区和常量数据区、动态链接库，可读写区有全局数据区、堆区、栈区

4.如何使用malloc()、free()函数，它们针对的哪一个区进行操作？为什么要对程序使用的内存进行管理？
答：首先要加入#include <stdlib.h>头文件再按照相应格式进行使用，针对堆区，如果只申请内存而不释放会导致内存泄露造成危害，也可以减少内存消耗


### 内存模型的应用

```c
#include <stdio.h>
#include <stdlib.h>

const int constValue = 100;
const char* constString = "Hello, World!";
int globalVar = 10;

void function(int arg) {
    int localVar = 20;
    int *ptr = malloc(sizeof(int));
    *ptr = 30;
    free(ptr);
}

int main() {
    static int staticVar = 40;
    int localVarMain = 50;
    function(60);
    return 0;
}
```
* constValue：存储在全局数据区且为常亮如果不在这个区不能被文件中的其它函数使用因其为常量也能防止其被篡改
* constString：存储在全局数据区且为常量字符串如果不在这个区不能被其他函数使用也可防止其被篡改
* globalVar：存储在全局数据区不为常量如果不在这个区不能被文件中的其它函数使用,它可以被文件中所有函数使用和修改
* staticVar：为静态变量存储在全局数据区，若不使用static关键字可能会被定义多次，也无法在全局中使用
* localVar：存储在栈区里为局部变量，只能在该函数内部使用，如果不在这个区能被文件中的其它函数使用
* ptr：为局部指针变量指向由malloc函数申请得到的内存，ptr存在栈区中ptr指向的区域存在堆区里，若不被free释放的话可以做到在全局中使用ptr指向的区域
* localVarMain：存储在栈区为局部变量只能在main函数中使用，如果不在这个区能被文件中的其它函数使用

### 浅谈Cache

 1. 什么是冯诺伊曼体系结构？什么是现代计算机的组织结构？这两者的不同点在哪里？
 答：冯洛依曼体系结构之前的计算机只能输入一条命令执行一条命令，而冯洛依曼体系结构增加了存储器实现了可以存入多条命令，其结构体系包含了运算器、控制器、存储器、输入设备、输出设备（由哔站王道考研计算机原理总结而出）。现代计算机组织结构一般包括主机（CPU、主存储器即内存）和外设（输入输出设备如键盘鼠标显示器，辅助存储器即硬盘）。其中冯洛依曼体系是以运算器为中心现代计算机是以存储器为中心，冯洛依曼体系运算器和控制器是分开的，现代计算机运算器和控制器放到一起形成了CPU

 2. 主存储器是如何工作的？
 答：主存储器包含了存储体、MAR、MDR。读取数据时，CPU将数据地址传入MAR，主存储器根据地址在存储体中找到相应数据再写入MDR，CPU再从MDR获取数据。写入数据时，CPU将写入地址和数据分别传给MAR、MDR，主存储器再根据CPU的信息将数据写入对应地址（由哔站王道考研计算机原理归纳得出）

 3. 什么是Cache的局部性原理？它包括哪些方面的内容？
 答：Cache 的局部性原理是指程序在执行时呈现出的一种对存储器访问的局部性倾向。这个原理是计算机系统中设计高速缓冲存储器（Cache）的重要依据。（查阅得）
    局部性包含时间局部性和空间局部性。
    空间局部性：在最近的未来要用到的信息（指令和数据），很可能与现在正在使用的信息在存储空间上是邻近的
    时间局部性：在最近的未来要用到的信息，很可能是现在正在使用的信息（由哔站王道考研计算机原理总结得出）

 4. Cache的运用为什么可以加快系统整体性能？
 答：主存储器的访问速度相对较慢，而 Cache 的速度则快得多，通常与CPU的速度更为接近
 存在局部性原理，Cache 可以预测哪些数据可能会被即将执行的指令访问，并提前将这些数据加载到Cache 中，而且其命中率高，从而减少访问主存储器的次数，降低访问延迟
 Cache可以与处理器一起工作，提高运行效率（由总结和查阅所得）

 #### 学习心得
 这道题考察计算机系统基础知识，面对一个个熟悉而又陌生的词汇，知道电脑里存在却又不知道其作用，我只能去就读哔哩哔哩大学深造寻求答案，接着我看了王道考研计算机原理从其中了解了计算机发展历程、基本组成、硬件介绍及原理，但没有更深层次地去了解更复杂的原理，后面又去零零散散搜了一些硬件内存讲解加深了理解。感觉这部分知识还是挺难理解的前面都是注重实践而这节是注重理论，需要的头脑感觉更多了。。。

 ### 代码优化
 
[ 代码与解题过程](代码优化.md)

# 总结感悟

心得感悟在每个题后面都有写，总之就是先学习再慢慢解题，继续加油吧

