### 1 左值、右值及其引用的区别

##### 概念

###### **左值**

- 左值既可以出现在等号左边，也能出现在等号右边
- 左值可以修改
- 左值是可寻址的变量，具有持久性

###### **纯右值**

- 右值只能出现在等号右边

- 右值不可修改
- 右值一般是不可寻址的常量，具有临时性

###### **将亡值**

- C++11新增的与右值引用相关的表达式
- 常用来完成移动构造或移动赋值的特殊任务，扮演着“将亡”的角色

- 将亡值定义了这样一种行为：具名的临时值、同时又能够被move。



##### 示例

###### **左值示例**

- 变量名、函数名以及数据成员名
- 返回左值引用的函数调用
- 由赋值运算符或复合赋值运算符连接的表达式，如(a=b, a-=b等)
- 解引用表达式*ptr
- 前置自增和自减表达式(++a, ++b)
- 成员访问（点）运算符的结果
- 由指针访问成员（ `->` ）运算符的结果
- 下标运算符的结果(`[]`)
- 字符串字面值("abc")

###### **纯右值示例**

- 字面值(字符串字面值除外)，例如1，'a', true等
- 返回值为非引用的函数调用或操作符重载，例如：str.substr(1, 2), str1 + str2, or it++
- 后置自增和自减表达式(a++, a--)
- 算术表达式
- 逻辑表达式
- 比较表达式
- lambda表达式

###### **将亡值示例**

- 返回右值引用的函数返回值，如static_cast<T&&>(t)

- 转换为右值引用的转换函数的返回值，如std::move(t)



##### 引用

###### **左值引用**

- 只能引用左值
- 通过&运算符声明

###### **右值引用**

- 只能引用右值

- 通过&&运算符声明



> **C++11的右值引用：**
>
> - 右值引用具有移动语义，可以通过std::move函数将资源的所有权（如动态分配的内存）从一个对象转移到另一个对象，避免了不必要的拷贝和内存分配。
> - 右值引用还使得实现“完美转发”成为可能，也就是将传入的参数原样传递给其他函数，避免了多次拷贝。





##### 文心一言版解释

> 左值引用和右值引用是C++中两种重要的概念，它们的区别和意义如下：
>
> 1. 左值引用和右值引用的区别：
>    - 基本概念：左值是可以放在赋值号左边，可以被赋值的值，它必须有内存实体；而右值是当在赋值号右边取出值赋给其他变量的值，它可以在内存也可以在CPU寄存器。
>    - 操作方式：左值引用是用来改变指针的指向，还可以改变指针所指向的值；右值引用可以用来给左值赋值。
> 2. 右值引用的意义：
>    - 提高效率：通过避免不必要的对象拷贝，节省运算存储资源，提高程序的执行效率。
>    - 简化操作：使得泛型编程更为简洁明确，便于操作。



##### ChatGPT版解释

> 1. 区别：
>
> - 左值引用（lvalue reference）：通过&符号来声明，只能引用左值（具有内存地址的表达式或变量），可以修改左值。
> - 右值引用（rvalue reference）：通过&&符号来声明，只能引用右值（临时或即将销毁的值），可以修改右值。
>
> 2. 右值引用的意义：
>
> - 资源移动：右值引用可以通过std::move函数，将一个对象的资源所有权从一个对象转移到另一个对象，避免了不必要的内存拷贝和分配。
> - 完美转发：右值引用允许我们实现所谓的“完美转发”，即将传入的参数原样传递给其他函数，避免了多次拷贝。
> - 优化性能：右值引用可以避免不必要的对象拷贝和析构，提高程序的性能和效率。



参考文章：

[深入理解左值和右值 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/513628368)





---



### 2 C++内存泄漏

##### 概念

内存泄漏（Memory Leak）是指程序中己动态分配的堆内存由于某种原因程序未释放或无法释放，造成系统内存的浪费，导致程序运行速度减慢甚至系统崩溃等严重后果。



##### 分类

内存泄漏分为以下两类：

- **堆内存泄漏**：我们经常说的内存泄漏就是堆内存泄漏，在堆上申请了资源，在结束使用的时候，没有释放归还给OS，从而导致该块内存永远不会被再次使用
- **资源泄漏**：通常指的是系统资源，比如socket，文件描述符等，因为这些在系统中都是有限制的，如果创建了而不归还，久而久之，就会耗尽资源，导致其他程序不可用

##### 根源

- **进程内存布局**

![image-20231010160523235](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231010160523235.png)

​	上图为32位进程的内存布局，从上图中主要包含以下几个块：

​	[内核空间](https://www.zhihu.com/search?q=内核空间&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})：供内核使用，存放的是内核代码和数据

​	stack：这就是我们经常所说的栈，用来存储[自动变量](https://www.zhihu.com/search?q=自动变量&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})([automatic variable](https://www.zhihu.com/search?q=automatic variable&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260}))

​	mmap:也成为[内存映射](https://www.zhihu.com/search?q=内存映射&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})，用来在进程[虚拟内存](https://www.zhihu.com/search?q=虚拟内存&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})地址空间中分配地址空间，创建和[物理内存](https://www.zhihu.com/search?q=物理内存&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})的映射关系

​	heap:就是我们常说的堆，动态内存的分配都是在堆上

​	bss:包含所有未初始化的全局和[静态变量](https://www.zhihu.com/search?q=静态变量&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})，此段中的所有变量都由0或者空[指针初始化](https://www.zhihu.com/search?q=指针初始化&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})，程序加载器在加载程序时为BSS段分配内存

​	data:初始化的数据块

- - 包含显式初始化的[全局变量](https://www.zhihu.com/search?q=全局变量&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})和静态变量

  - 此段的大小由[程序源代码](https://www.zhihu.com/search?q=程序源代码&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})中值的大小决定，在运行时不会更改

  - 它具有读写权限，因此可以在运行时更改此段的变量值

  - 该段可进一步分为[初始化只读区](https://www.zhihu.com/search?q=初始化只读区&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})和初始化读写区


​	text：也称为文本段

- - 该段包含已编译程序的[二进制文件](https://www.zhihu.com/search?q=二进制文件&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})。
  - 该段是一个只读段，用于防止程序被意外修改
  - 该段是可共享的，因此对于[文本编辑器](https://www.zhihu.com/search?q=文本编辑器&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})等频繁执行的程序，内存中只需要一个副本

- **内存分配**

  - **静态分配**

    编译时分配。包括:全局、静态全局、静态局部三种变量。

  - **动态分配**

    堆上通过malloc函数进行内存的动态分配，通过malloc（new）函数分配的内存需要开发人员通过free（delete）函数手动释放，因此容易发生内存泄漏

    栈上通过alloca函数进行内存的动态分配，通过alloca函数分配的内存由编译器进行释放，无需手动操作


- **堆和栈的区别**

​	**空间大小不同**

​	一般来讲在 32 位系统下，堆内存可以达到4G的空间，从这个角度来看堆内存几乎是没有什么限制的。

​	对于栈来讲，一般都是有一定的空间大小的，一般依赖于操作系统(也可以人工设置)

​	**能否产生碎片不同**

​	对于堆来讲，频繁的内存分配和释放势必会造成内存空间的不连续，从而造成大量的碎片，使程序效率降低。

​	对于栈来讲，内存都是连续的，申请和释放都是指令移动，类似于[数据结构](https://www.zhihu.com/search?q=数据结构&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})中的进栈和出栈

​	**增长方向不同**

​	对于堆来讲，生长方向是向上的，也就是向着内存地址增加的方向

​	对于栈来讲，它的生长方向是向下的，是向着内存地址减小的方向增长

​	**分配方式不同**

​	堆都是动态分配的，比如我们常见的malloc/new；而栈则有静态分配和动态分配两种。

​	静态分配是编译器完成的，比如局部变量的分配，而栈的动态分配则通过alloca()函数完成

​	二者动态分配是不同的，栈的动态分配的内存由编译器进行释放，而堆上的动态分配的内存则必须由开发人自行释放

​	**分配效率不同**

​	栈有操作系统分配专门的[寄存器](https://www.zhihu.com/search?q=寄存器&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})存放栈的地址，压栈出栈都有专门的指令执行，这就决定了栈的效率比较高

​	堆内存的申请和释放专门有运行时库提供的函数，里面涉及复杂的逻辑，申请和释放效率低于栈



##### 内存泄漏产生方式

- 常发性内存泄漏

​	产生内存泄漏的代码或者函数会被多次执行到，在每次执行的时候，都会产生内存泄漏

- 偶发性内存泄漏

​	偶发性内存泄漏函数只在特定的场景下才会被执行。

- 一次性内存泄漏

​	偶发性内存泄漏函数只在特定的场景下才会被执行。

- 隐式内存泄漏

​	程序在运行过程中不停的分配内存，但是直到结束的时候才释放内存。



##### 内存泄漏产生原因

- **未释放**

如下代码所示，申请内存后，没有调用free函数进行释放，造成内存泄漏

```C
int fun() {
    char * pBuffer = malloc(sizeof(char));
    
    /* Do some work */
    return 0;
}
```

或者是下面代码中，析构函数没有释放掉为成员变量申请的动态内存

```C
class Obj {
 public:
   Obj(int size) {
     buffer_ = new char;
   }
   ~Obj(){}
  private:
   char *buffer_;
};

int fun() {
  Object obj;
  // do sth
  return 0;
}
```

- **未匹配**

  - new和free

  ```C
  Test *t = new Test;
  free(t)
  ```

  此处会产生内存泄漏。new操作符会先通过operator new分配一块内存，然后在该块内存上调用placement new即调用Test的构造函数。而在上述代码中，只是通过free函数释放了内存，但是没有调用Test的析构函数以释放Test的成员变量，从而引起内存泄漏。

  - new[]和delete

  ```C
  int main() {
    Test *t = new Test [10];
    // do sth
    delete t;
    return 0;
  }
  ```

  在上述代码中，我们通过new创建了一个Test类型的数组，然后通delete操作符删除该数组，编译并执行，输出如下：

  ```C
  in Test
  in Test
  in Test
  in Test
  in Test
  in Test
  in Test
  in Test
  in Test
  in Test
  in ~Test
  ```

  从上面输出结果可以看出，调用了10次构造函数，但是只调用了一次析构函数，所以引起了内存泄漏。这是因为调用delete t释放了通过operator new[]申请的内存，即malloc申请的内存块，且只调用了t[0]对象的析构函数，t[1..9]对象的析构函数并没有被调用。

- **虚析构**

在C++中，虚析构函数是一种特殊的析构函数，用于在派生类对象被删除时正确释放资源。当基类指针指向派生类对象并删除该指针时，如果基类的析构函数不是虚构函数，那么只会调用基类的析构函数，而不会调用派生类的析构函数，这可能导致派生类特有的资源无法正确释放，造成内存泄漏。

为了解决这个问题，可以将基类的析构函数声明为虚析构函数，即在基类的析构函数前加上关键字"virtual"。这样，当基类指针指向派生类对象并删除该指针时，会先调用派生类的析构函数，然后再调用基类的析构函数，从而正确释放派生类特有的资源。

虚析构函数的一般语法如下：

```cpp
class Base {
public:
    virtual ~Base() {
        // 基类析构函数的实现
    }
};

class Derived : public Base {
public:
    ~Derived() override {
        // 派生类析构函数的实现
    }
};
```

> 需要注意的是，派生类的析构函数可以选择是否加上"override"关键字，但建议加上以提高代码的可读性和可维护性。同时，由于虚析构函数会增加额外的开销，只有在需要通过基类指针删除派生类对象时才需要使用虚析构函数。

**存在继承关系的情况下，构造函数和析构函数的调用顺序**

​	派生类对象在创建时构造函数调用顺序：

1. 调用父类的构造函数
2. 调用父类成员变量的构造函数
3. 调用派生类本身的构造函数

​	派生类对象在析构时的析构函数调用顺序：

1. 执行派生类自身的析构函数
2. 执行派生类成员变量的析构函数
3. 执行父类的析构函数



- **循环引用**

​		循环引用问题在C++中是指当两个或多个对象互相持有对方的引用（通常是通过[智能指针](https://so.csdn.net/so/search?q=智能指针&spm=1001.2101.3001.7020)），导致它们的引用计数永远不会降为零，从而导致内存泄漏的情况。这种问题在使用`shared_ptr`时尤为突出，因为`shared_ptr`会自动管理对象的生命周期并维护引用计数。

​		比如：

```C
class A {
public:
    std::shared_ptr<B> b_ptr;
};
 
class B {
public:
    std::shared_ptr<A> a_ptr;
};
```

​		当创建A,B对象时，并使他们相互引用

```C
std::shared_ptr<A> a = std::make_shared<A>();
std::shared_ptr<B> b = std::make_shared<B>();
a->b_ptr = b;
b->a_ptr = a;
```

​		在这个例子中，A对象持有B对象的`shared_ptr`，B对象持有A对象的`shared_ptr`。这会导致循环引用，因为A和B的引用计数都为1，它们都不会被自动销毁。这种情况下，即使`shared_ptr`超出其作用域，相关对象也不会被释放，从而导致[内存泄漏](https://so.csdn.net/so/search?q=内存泄漏&spm=1001.2101.3001.7020)。

为了解决循环引用问题，我们可以将B类中的`shared_ptr<A>`替换为`weak_ptr<A>`，这样就可以打破循环引用：

```C
class B {
public:
    std::weak_ptr<A> a_ptr;
};
```

> 需要注意的是，`weak_ptr`无法直接访问其指向的对象。要访问对象，必须先将`weak_ptr`转换为`shared_ptr`，这可以通过`lock()`成员函数实现。同时，在访问之前，可以使用`expired()`成员函数检查`weak_ptr`是否悬空，以确保安全访问。

##### 如何避免内存泄漏

- **避免在堆上分配**

​		大部分的内存泄漏都是在堆上分配引起的，但如果实际应用场景中可以不用到堆，比如对象可以在栈上分配，一方面栈的效率高于堆，另一方面，还能避免内存泄漏。

- **手动释放**
  - 对于[malloc函数](https://www.zhihu.com/search?q=malloc函数&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})分配的内存，在结束使用的时候，使用free函数进行释放
  - 对于new操作符创建的对象，切记使用delete来进行释放
  - 对于new []创建的对象，使用delete[]来进行释放(使用free或者delete均会造成内存泄漏)
- **避免使用裸指针**
- **使用STL库**

​	如std::vector或std::array代替C语言数组，使用std::string代替char*等

- **智能指针**

  - unique_ptr

    unique_ptr是限制最严格的一种智能指针，用来替代之前的auto_ptr，独享被管理对象指针所有权。当unique_ptr对象被销毁时，会在其析构函数内删除关联的原始指针。

  - shared_ptr

    shared_ptr是共享管理权，即多个shared_ptr可以共用同一块关联对象，其内部采用的是引用计数，在拷贝的时候，引用计数+1，而在某个对象退出作用域或者释放的时候，引用计数-1，当引用计数为0的时候，会自动释放其管理的对象。

  - weak_ptr

    weak_ptr是智能指针中最弱的一个，weak_ptr只能访问所指向的内存区域，当weak_ptr生命周期结束的时候，其所指向的内存依旧完好无损。weak_ptr主要与shared_ptr一起使用。

    > 引用计数指针的本质是”在内存需要析构时，引用及技术指针的计数值不断的减1直到为0，从而对内存进行析构“，但是weak_ptr不会影响shared_ptr引用计数指针的计数值，即weak_ptr不会影响到只想去与内存的生命周期
    >
    > 因此这也就是weak_ptr不会导致无限循环释放的错误；

- **RAII**

​	RAII是Resource Acquisition is Initialization(资源获取即初始化)的缩写，是C++语言的一种管理资源，避免泄漏的用法。

​	利用的就是C++构造的对象最终会被销毁的原则。利用C++对象生命周期的概念来控制程序的资源,比如内存,[文件句柄](https://www.zhihu.com/search?q=文件句柄&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260}),网络连接等。

​	RAII的做法是使用一个对象，在其构造时获取对应的资源，在对象生命周期内控制对资源的访问，使之始终保持有效，最后在对象析构	的时候，释放构造时获取的资源。

​	简单地说，就是把资源的使用限制在对象的生命周期之中，自动释放。



##### 如何定位内存泄漏

- 日志

​		这种方案的核心思想，就是在每次分配内存的时候，打印指针地址，在释放内存的时候，打印内存地址，这样在程序结束的时候，通过分配和释放的差，如果分配的条数大于释放的条数，那么基本就能确定程序存在内存泄漏，然后根据日志进行详细分析和定位。

```C
char * fun() {
  char *p = (char*)malloc(20);
  printf("%s, %d, address is: %p", __FILE__, __LINE__, p);
  // do sth
  return p;
}

int main() {
  fun();
  
  return 0;
}
```

- **统计**

​	统计方案可以理解为日志方案的一种特殊实现，其主要原理是在分配的时候，统计分配次数，在释放的时候，则是统计释放的次数，这样在程序结束前判断这俩值是否一致，就能判断出是否存在内存泄漏。

​	此方法可帮助跟踪已分配内存的状态。为了实现这个方案，需要创建三个自定义函数，一个用于内存分配，第二个用于内存释放，最后一个用于检查内存泄漏。

- **工具**

  - valgrind

  - mtrace

  - dmalloc

  - ccmalloc

  - memwatch

  - debug_new

##### 参考文章

[C++ 怎么检测内存泄露，怎么定位内存泄露？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/63946754/answer/2973245260)

[深入理解C++中的循环引用问题及解决方法_c++循环引用_adam2021的博客-CSDN博客](https://blog.csdn.net/adam2021/article/details/129849022)



---

###  3 const char *p 、 char const *p 与 char * const p的区别

const修饰的位置不同，会对指针p的性质产生不同的影响。

##### const char *p

指针p指向的是一个常量字符，即p所指向的字符内容不能通过指针p来修改，但是指针p可以指向其他的字符。

例如：

```C++
const char *p = "Hello"; // p指向一个字符串常量，不能通过p修改字符内容
```

p = "World"; // 可以将p指向"World"

##### char const *p

与const char *p相同，指针p也指向的是一个常量字符，即p所指向的字符内容不能通过指针p来修改，但是指针p可以指向其他的字符。

##### char * const p

指针p本身是一个常量指针，即p的值不能改变，它始终指向一个固定的地址，但是该地址中的字符内容可以通过指针p进行修改。

例如：

```c++
char str1[] = "Hello";
char * const p = str1; // p是一个常量指针，始终指向str1的地址
p[0] = 'h'; // 可以通过p来修改str1的字符内容
char str2[] = "World";
//p = str2; // 不能修改指针p的值，会报错
```



> 总结：const char *p 和 char const *p 是等价的，指针p指向的是一个常量字符；而char * const p是一个常量指针，指针p自身的值不能改变，但是可以通过指针p来修改字符内容。





---



### 4 static_cast、dynamic_cast、reinterpret_cast 和 const_cast的区别

##### static_cast

用于基本类型之间的强制类型转换，以及具有继承关系的指针或引用之间的转换。它在**编译时**进行类型检查，不会进行dynamic_cast所需的运行时类型检查。

##### dynamic_cast

用于具有继承关系的指针或引用之间的转换，可以在**运行时**检查对象的类型是否能够转换成功。它只能用于具有虚函数的类（即多态类型），可以在将多态类型的指针或引用转换为其派生类指针或引用时使用。如果转换是不安全的，dynamic_cast返回一个空指针（对指针）或抛出一个std::bad_cast异常（对引用）。

##### reinterpret_cast

用于不同类型的指针、引用或任意类型之间的转换，它提供了一种底层的重新解释目标类型的方法，但是很容易导致未定义行为。reinterpret_cast不会进行任何类型检查，因此需要谨慎使用。

##### const_cast

用于添加或移除const、volatile修饰符，通常用于去除const限定，使一个const对象变为非常量。const_cast只能操作指针或引用，不能用于按值的变量。需要注意的是，const_cast只能改变底层对象的const性质，不能用于改变底层对象类型。



---



### 5 C++静态多态和动态多态

C++中的多态是指同一函数可以根据调用的对象的实际类型来执行不同的操作。多态通过基类指针或引用调用派生类对象的成员函数，实现对不同类型的对象的统一操作。

C++中的多态有两种实现方式：编译时多态（静态多态）和运行时多态（动态多态）。

1. 编译时多态（静态多态）：
   - 函数重载：在同一作用域内，相同函数名但参数列表不同的函数可以有多个版本，编译器根据实参的类型来决定调用哪个函数。
   - 模板：使用通用的算法实现，根据实际参数的类型在编译时将相应的代码生成。
2. 运行时多态（动态多态）：
   - 虚函数（虚函数重写）：使用基类的指针或引用调用派生类的重写函数，实际调用的是派生类中与基类虚函数同名的函数。通过将基类的函数声明为虚函数，在运行时确定调用的是哪个类的函数，实现多态性。
   - 动态绑定：使用基类指针或引用调用派生类对象的函数时，会在运行时动态绑定到正确的成员函数上，实现函数的动态调用。



---



### 6 enable_shared_from_this是什么

##### 概述

`enable_shared_from_this` 是 C++ 标准库提供的一个模板类，位于 `<memory>` 头文件中。它用于解决由于对象被 `shared_ptr` 管理内存导致的问题，即在对象中获取自身的 `shared_ptr`。

当某个类继承自 `enable_shared_from_this` 时，该类的对象可以通过调用`shared_from_this()`函数来获取一个指向自己的 `shared_ptr`。这样，在对象被 `shared_ptr` 管理时，可以避免手动创建一个新的 `shared_ptr` 导致的内存泄漏或对象的多个 `shared_ptr` 不一致的问题。

使用 `enable_shared_from_this` 的关键是避免直接通过构造函数创建一个 `shared_ptr`，而是通过类内部的成员函数获取指向自身的 `shared_ptr`。这样可以确保 `shared_ptr` 的计数器被正确维护，避免多个 `shared_ptr` 对同一个对象进行资源管理而导致的问题。

使用 `enable_shared_from_this` 可以更方便地在对象中获取到指向自身的 `shared_ptr`，避免手动管理对象的生命周期，提高代码	的可维护性和安全性。

##### 使用条件

1. 对象必须是由 `shared_ptr` 管理的，不能使用 `unique_ptr` 或者原始指针。
2. 在对象中不能直接或间接创建一个新的 `shared_ptr`，否则会引发 `std::bad_weak_ptr` 异常。
3. 调用 `shared_from_this()` 时，必须保证对象已经被至少一个 `shared_ptr` 管理，否则会导致 `std::bad_weak_ptr` 异常的发生。



##### 使用步骤

要使用 `enable_shared_from_this`，需要遵循以下步骤：

1. 定义一个类，并使其继承自 `std::enable_shared_from_this`：

```c++
class MyClass : public std::enable_shared_from_this<MyClass> {
  // 类的成员和方法
};
```

2. 在需要获取指向自身的 `shared_ptr` 的地方，调用 `shared_from_this()` 方法：

```c++
std::shared_ptr<MyClass> obj = std::make_shared<MyClass>();
std::shared_ptr<MyClass> sharedPtr = obj->shared_from_this();
```



---

### 7 常量指针和指针常量的区别

1. 常量指针（Const Pointer）：
   常量指针是指向常量的指针。它不能改变所指向的值，但可以改变指针本身的值，也就是让它指向其他的内存地址。例如：

```cpp
const int *p = &a; // p指向a，不能通过p改变a的值，但可以改变p指向的其他变量
```

这意味着，你可以改变指针 `p` 所指向的地址，但不能通过 `p` 来改变它所指向的变量的值。

2. 指针常量（Pointer Constant）：
   指针常量是指针本身是常量，即指针不能改变，而指针所指向的值可以改变。例如：

```cpp
int * const p = &a; // p是一个常量指针，不能改变p的值，但可以改变p所指向的变量的值
```

这意味着，你不能改变指针 `p` 的地址（即不能让 `p` 指向其他的变量），但可以通过 `p` 来改变它所指向的变量的值。

总结一下，"常量指针"是一个指向常量的指针，它不能通过这个指针来改变所指向的变量的值；而"指针常量"则是一个指针本身是常量，即它不能指向其他的变量，但可以通过这个指针来改变它所指向的变量的值。



---



### 8 为什么auto_ptr会被弃用

`auto_ptr` 在 C++98 标准中是一个常用的智能指针类型，它可以在超出作用域时自动删除它所拥有的对象。然而，它在 C++11 中被弃用，并在 C++17 中被移除，主要有以下几个原因：

1. **所有权问题**：`auto_ptr` 的一个主要问题是它不能很好地支持STL容器。当多个 `auto_ptr` 指向同一个对象时，删除其中一个并不会删除其他 `auto_ptr` 所指向的对象，这导致了内存泄漏。这是因为在 C++98 中，`auto_ptr` 并不支持所有权的概念。
2. **不适用于数组**：`auto_ptr` 在处理动态分配的数组时也有问题。当 `auto_ptr` 被删除时，它只会删除第一个元素，而不会删除整个数组，这可能导致内存泄漏。
3. **不适用于自定义类型**：`auto_ptr` 在处理自定义类型时可能会出现问题。如果一个类型的析构函数抛出一个异常，那么 `auto_ptr` 会删除它所指向的对象，然后再删除一次，这可能导致未定义的行为。
4. **所有权转移**：`auto_ptr` 不支持所有权转移。这意味着你不能把一个 `auto_ptr` 的所有权从一个变量转移到另一个变量。这限制了 `auto_ptr` 的使用场景。

为了解决上述问题，C++11引入了新的智能指针类型 `std::unique_ptr` 和 `std::shared_ptr`。这两个类型可以更好地管理动态分配的内存，支持所有权的概念，并且可以很好地与 STL 容器集成。因此，`auto_ptr` 被弃用并最终从标准库中移除也就理所当然了



---



### 9 new/delete与malloc/free的区别

`new` 和 `delete` 是 C++ 中用于动态分配和释放对象的运算符，而 `malloc` 和 `free` 是 C 语言中用于动态分配和释放内存的函数。它们之间存在以下几个区别：

1. **类型安全性**：`new` 和 `delete` 是类型安全的，它们会根据类型进行内存分配和释放，并在分配对象时调用构造函数，在释放对象时调用析构函数。而 `malloc` 和 `free` 是无类型的，它们只是在字节级别进行内存分配和释放，不会调用构造函数和析构函数。
2. **内存分配大小**：`new` 运算符根据指定的类型自动计算所需的内存大小，而 `malloc` 函数需要手动指定所需的字节数。例如，`new int` 分配一个 `int` 大小的内存，而 `malloc(sizeof(int))` 需要手动指定所需的字节数。
3. **异常处理**：`new` 运算符在分配内存失败时，会抛出 `std::bad_alloc` 异常，以便于异常处理。而 `malloc` 函数在分配内存失败时，返回 `NULL`。
4. **自定义类型支持**：`new` 可以分配自定义类型的内存，并在分配时调用构造函数。而 `malloc` 只能分配字节大小的内存块。

总之，`new` 和 `delete` 是 C++ 中的运算符，提供了更高级、类型安全和自动处理的内存分配和释放机制。而 `malloc` 和 `free` 则是 C 语言中的函数，更为底层、无类型且手动指定内存字节数。建议在 C++ 中优先使用 `new` 和 `delete`，除非有特定的兼容性或其他原因需要使用 `malloc` 和 `free`。



---



### 10 内联函数是怎么实现的，有什么优缺点

##### 概念

内联函数是一种编译器优化技术，用于将函数的代码直接插入到函数调用的地方，而不是通过函数调用的方式进行执行。

##### 实现

内联函数的实现方式主要包括以下两个步骤：

1. 内联声明：在函数声明前加上 `inline` 关键字，告诉编译器将该函数作为内联函数进行处理。内联声明通常放在头文件中，以便在需要时在多个编译单元间共享。
2. 内联展开：编译器在编译过程中遇到内联函数的调用时，将函数的定义直接插入到调用处，替代函数调用的过程。这样，函数的执行就不再通过跳转到函数体的方式，而是直接在调用地点展开执行。

##### 优点

1. 减少函数调用开销：内联函数的内部代码直接嵌入到调用处，避免了函数调用的开销，如栈帧的创建与销毁、参数传递和跳转。
2. 提高代码执行速度：由于内联函数的代码直接展开，消除了函数调用时的跳转和返回过程，可以减少指令跳转带来的延迟，从而提高代码的执行速度。
3. 可以进行更多的编译器优化：内联函数在展开时，编译器能够对其进行更多的优化，如常量折叠、循环展开、无用代码消除等。

##### 缺点

1. 增大代码体积：由于内联函数在调用处展开，会增加代码的重复性，导致可执行文件的体积增大。
2. 增加编译时间：内联函数在每个调用处都需要重新展开，会增加编译时间，尤其是对于大型项目。
3. 可能导致缓存失效：代码展开可能导致指令和数据的增加，使得缓存命中率下降，影响代码的执行效率。

> 在实际使用中，适合内联的函数通常是短小且频繁调用的函数。



---

### 11 this指针

在C++中，`this` 是一个指向当前对象的指针。它是每个非静态成员函数（包括成员函数、成员方法和成员重载运算符）的一个隐式参数，用于指向调用该函数的对象本身。

`this` 指针的作用是允许在类的成员函数中访问和操作当前对象的成员变量和成员函数。通过 `this` 指针，可以区分对象的成员和同名的局部变量或参数，避免命名冲突。

当调用一个对象的成员函数时，编译器会自动将对象的地址传递给 `this` 指针。通过 `this` 指针，可以访问对象的成员变量和成员函数，如 `this->member`（`(*this).member`） 或 `this->method()`。

以下是 `this` 指针的一些特点和使用注意事项：

1. `this` 指针是指向当前对象的常量指针，不允许修改其值。因此，不能将 `this` 指针赋值给其他指针变量。
2. `this` 指针只能在非静态成员函数中使用，静态成员函数没有 `this` 指针。
3. `this` 指针可以在成员函数中进行显式引用，也可以省略不写，编译器会自动添加。
4. `this` 指针可以作为隐式参数传递给其他函数，以便在其他函数中访问当前对象。

使用 `this` 指针可以在类的成员函数中方便地访问和操作对象的成员，特别是在处理成员变量和成员函数名称冲突时非常有用。

> 在C++中，`this` 是一个指向当前对象的指针，它是一个**右值**（rvalue）。
>
> 由于 `this` 是一个右值，因此不能对它进行赋值或修改。它只能用于访问成员
