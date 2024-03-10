

## 一、编译优化

### 1. 调用GCC优化

`#pragma GCC optimize(2);`

`#pragma GCC optimize(2)` 是GCC编译器的一个编译优化指令。它告诉编译器以优化级别2来进行编译优化。 GCC编译器提供了一系列的编译优化级别选项，其中级别2是比较高的优化级别。

编译器的优化级别决定了编译器在编译过程中进行的优化程度。较高的优化级别可能会产生更高效的代码，但同时也可能导致编译时间增加。

需要注意的是，`#pragma GCC optimize(2)` 是GCC编译器特有的指令，可能不适用于其他编译器。如果需要在其他编译器中进行类似的优化设置，可能需要使用其他编译器提供的相应指令或选项。

## 二、输入输出

### 1. 解除输入输出同步

`ios::sync_with_stdio(0);`

在默认情况下，C++的输入输出流(`cin`和`cout`)与C标准库的输入输出流(`stdin`和`stdout`)是同步的，即它们共享相同的输入输出缓冲区.这种同步机制可以确保在使用C++输入输出流时，与C标准库的输入输出函数(`scanf`和`printf`)混合使用时不会出现问题。

然而，由于C++输入输出流的实现比C标准库的输入输出函数要慢，特别是在大量输入输出操作时，这种同步机制可能会导致性能下降。为了提高输入输出的速度，可以使用`std::ios::sync_with_stdio(0)`函数将C++输入输出流与C标准库的输入输出流解除同步。

### 2. 解除cin，cout绑定关系

`cin.tie(0),cout.tie(0);`

`cin.tie(0)`和`cout.tie(0)`是用于解除`cin`和`cout`的绑定的函数。默认情况下，`cin`和`cout`是绑定在一起的，这意味着在进行输入操作时，输出缓冲区会被刷新。通过使用`cin.tie(0)`和`cout.tie(0)`函数，可以解除它们之间的绑定关系，提高输入输出的速度。

### 3. 自定义快读快写函数

-   读

```cpp
int GetInt() {
    int res = 0;
    int op=1;//负号标记
    int ch;
    ch = getchar();
    while (!isdigit(ch)) {
        if (ch == '-') op = -1;
        ch = getchar();
    }
    while (isdigit(ch)) {
        res = res * 10 + (ch - '0');
        ch = getchar();
    }
    return op * res;
}
```

-   写

```cpp
void PutInt(int res) {
    char s[100];//模拟stack实现倒序输出
    if (res < 0) {
        putchar('-');
        res = -res;
    }
    else if (res == 0) { 
        putchar('0');
        return; 
    }
    int top = 0;
    while (res > 0) {
        s[top]=putchar((res % 10) + '0');
        ++top;
        res /= 10;
    }
    do {
        --top;
        putchar(s[top]);
    } while (top > 0);
}
```

## 三、高精度算法

结构体定义

```cpp
struct hugeInt {
    int num[505] = { 0 };//数组长度根据题目要求开,倒序存储
    int len;
    string s = "";//负数的符号
};
```

字符串转高精度

```cpp
hugeInt toHugeInt(string n) {//将字符串转为高精度数
    hugeInt ans;
    int j = 0;//倒序的下标
    for (int i = n.size() - 1; i >= 0; i--) {
        j += 1;
        ans.num[j] = n[i] - '0';//数字从1->len,逆序存储
    }
    ans.len = n.size();//更新长度
    return ans;
}
```

比较

```cpp
int mycmp(hugeInt a, hugeInt b) {//判断两数大小
    if (a.len > b.len) return 1;
    else if (a.len < b.len) return -1;
    else {
        for (int i = a.len; i>= 1; i--) {
            if (a.num[i] > b.num[i]) return 1;
            else if (a.num[i] < b.num[i]) return -1;
        }
        return 0;
    }
}
```

去除前导0

```cpp
hugeInt preheadZero(hugeInt n) {
    while (n.num[n.len] == 0 && n.len > 1) n.len--;//处理前导0，因为数组逆序存储，所以依次往左删除
    return n;
}
```

打印

```cpp
inline void printInt(hugeInt n) {
    for (int i = n.len; i >= 1; i--) cout << n.num[i];
    cout << endl;
    return;
}
```

### 加法

```cpp
hugeInt add(hugeInt a,hugeInt b) {
    hugeInt ans;
    ans.len = max(a.len, b.len);
    for (int i = 1; i <= ans.len; i++) {
        ans.num[i] += a.num[i] + b.num[i];//逐位相加
        ans.num[i + 1] = ans.num[i] / 10;//进位处理
        ans.num[i] %= 10;//保留相加后的个位
    }
    if (ans.num[ans.len + 1] > 0) ans.len += 1;//处理最后的进位
    return ans;
}
```

### 减法

```cpp
hugeInt sub(hugeInt a,hugeInt b) {
    hugeInt ans;
    ans.len = a.len;
    if (mycmp(a, b) == -1) {//保证a>=b，从而用a-b
        swap(a, b);
        ans.s = "-";
    }
    for (int i = 1; i <= ans.len; i++) {
        ans.num[i] += a.num[i] - b.num[i];
        //将减完的结果加入该位，因为该位可能有前一位的借位，所以是 +=
        if (ans.num[i] < 0) {//处理借位
            ans.num[i] += 10;
            ans.num[i + 1] = -1;
        }
    }
    ans = preheadZero(ans);//处理前导0
    return ans;
    
}
```

### 乘法

```cpp
hugeInt mult(hugeInt a,hugeInt b) {
    hugeInt ans;
    ans.len = a.len + b.len - 1;
    for (int i = 1; i <= a.len; i++) {
        for (int j = 1; j <= b.len; j++) {
            ans.num[i + j - 1] += a.num[i] * b.num[j];//对应位置相乘
            ans.num[i + j] = ans.num[i + j - 1] / 10;//进位
            ans.num[i + j - 1] %= 10;//保留个位
        }
    }
    if (ans.num[ans.len + 1] > 0) ans.len += 1;//处理最后一个进位
    ans = preheadZero(ans);//处理前导0
    return ans;

}
```

### 除法

```cpp
hugeInt div(hugeInt a,ll b) {
    hugeInt ans;
    ans.len = a.len;
    for (int i = a.len; i >= 1; i--) {//除法与其他的不一样。被除数需要从高位开始除
        ans.num[i] = a.num[i] / b;//对应位相除
        a.num[i - 1] += a.num[i] % b*10;//余数处理
    }
    ans = preheadZero(ans);
    return ans;
}
```

### 阶乘

```cpp
hugeInt factorial(const int N) {
    hugeInt ans;
    ans.num[1] = 1;
    ans.len = 1;
    if (N < 0) {
        cout << "something error!" << endl;
        return ans;
    }
    for (int i = 2; i <= N; ++i) {
        int t = 0;//进位
        for (int j = 1; j <= ans.len; j++) {
            ans.num[j] = ans.num[j] * i+t;
            t = ans.num[j] / 10;
            ans.num[j] %= 10;
        }
        while (t) {
            ans.len++;
            ans.num[ans.len] = t % 10;
            t /= 10;
        }
    }
    return ans;
}
```

## 四、内存管理

不同的编程语言各自的内存管理方式。

- C/C++这种内存堆空间的申请和释放完全靠自己管理
- Java 依赖JVM来做内存管理，不了解jvm内存管理的机制，很可能会因一些错误的代码写法而导致内存泄漏或内存溢出
- Python内存管理是由私有堆空间管理的，所有的python对象和数据结构都存储在私有堆空间中。程序员没有访问堆的权限，只有解释器才能操作。

c++程序运行时所需内存空间

1. 固定部分 

- 代码区：存储二进制代码
- 数据区：全局变量，静态变量，常量等

2. 可变部分

- 栈区：运行方法的形参，局部变量，返回值，以及递归栈所需的空间。系统自动分配与回收
- 堆区：动态开辟的空间，存放new出来的对象在堆区的真实数据。需要手动回收

固定部分的内存消耗 是不会随着代码运行产生变化的， 可变部分则是会产生变化的

更具体一些，一个由C/C++编译的程序占用的内存分为以下几个部分：

- 栈区(Stack) ：由编译器自动分配释放，存放函数的参数值，局部变量的值等，其操作方式类似于数据结构中的栈。
- 堆区(Heap) ：一般由程序员分配释放，若程序员不释放，程序结束时可能由OS收回
- 未初始化数据区(Uninitialized Data)： 存放未初始化的全局变量和静态变量
- 初始化数据区(Initialized Data)：存放已经初始化的全局变量和静态变量
- 程序代码区(Text)：存放函数体的二进制代码

代码区和数据区所占空间都是固定的，而且占用的空间非常小，那么看运行时消耗的内存主要看可变部分。

在可变部分中，栈区间的数据在代码块执行结束之后，系统会自动回收，而堆区间数据是需要程序员自己回收，所以也就是造成内存泄漏的发源地。

**而Java、Python的话则不需要程序员去考虑内存泄漏的问题，虚拟机都做了这些事情**。

**内存对齐**

原因：

1. 平台原因：不是所有的硬件平台都能访问任意内存地址上的任意数据，某些硬件平台只能在某些地址处取某些特定类型的数据，否则抛出硬件异常。为了同一个程序可以在多平台运行，需要内存对齐。
2. 硬件原因：经过内存对齐后，CPU访问内存的速度大大提升。

CPU读取内存不是一次读取单个字节，而是**一块一块的读取**，块的大小可以是2，4，8，16个字节，具体取多少个字节取决于硬件。

因此对齐后的内存，会更方便CPU的寻址，优化运行速度

## 五，常用的头文件

```c++
#include <bits/stdc++.h> // 万能头文件，vs不可用
```

## 六，蓝桥杯特辑

```c++
#include <bits/stdc++.h>// 万能头文件
#pragma GCC optimize(2);// 调用GCC优化
#define endl '\n'

using namespace std;
// int 均声明为long long 或 int64_t
// 遇到题目优先采用暴力解法或shen'sou
int main(){
    ios::sync_with_stdio(0),cin.tie(0),cout.tie(0);
    
    
    return 0;
}
```

