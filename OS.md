



### 1 缓存算法

缓存算法是计算机科学中用于管理缓存中数据存储和替换的算法。缓存是一种用于临时存储数据的高速存储器，它可以减少对慢速存储器（如磁盘或网络）的访问次数，从而提高数据访问速度。

常见的缓存算法包括：

1. 先进先出（FIFO）：按照数据进入缓存的先后顺序进行替换。最早进入缓存的数据会被最先替换。
2. 最近最少使用（LRU）：根据数据项最近被使用的时间进行替换。最近使用较少的数据项会被优先替换。
3. 最不经常使用（LFU）：根据数据项被使用的频率进行替换。使用频率较低的数据项会被优先替换。
4. 最近使用的（MRU）：与LRU相反，根据数据项最近被使用的时间进行替换。最近使用的数据项会被优先替换。
5. 随机替换（Random）：随机选择要替换的数据项。



---



### 2 进程内存布局



32位进程的内存布局主要包含以下几个块：

[内核空间](https://www.zhihu.com/search?q=内核空间&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})：供内核使用，存放的是内核代码和数据

stack：这就是我们经常所说的栈，用来存储[自动变量](https://www.zhihu.com/search?q=自动变量&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})([automatic variable](https://www.zhihu.com/search?q=automatic variable&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260}))

mmap:也成为[内存映射](https://www.zhihu.com/search?q=内存映射&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})，用来在进程[虚拟内存](https://www.zhihu.com/search?q=虚拟内存&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})地址空间中分配地址空间，创建和[物理内存](https://www.zhihu.com/search?q=物理内存&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})的映射关系

heap:就是我们常说的堆，动态内存的分配都是在堆上

bss:包含所有未初始化的全局和[静态变量](https://www.zhihu.com/search?q=静态变量&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})，此段中的所有变量都由0或者空[指针初始化](https://www.zhihu.com/search?q=指针初始化&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})，程序加载器在加载程序时为BSS段分配内存

data:初始化的数据块

- 包含显式初始化的[全局变量](https://www.zhihu.com/search?q=全局变量&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})和静态变量
- 此段的大小由[程序源代码](https://www.zhihu.com/search?q=程序源代码&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})中值的大小决定，在运行时不会更改
- 它具有读写权限，因此可以在运行时更改此段的变量值
- 该段可进一步分为[初始化只读区](https://www.zhihu.com/search?q=初始化只读区&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})和初始化读写区

text：也称为文本段

- 该段包含已编译程序的[二进制文件](https://www.zhihu.com/search?q=二进制文件&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})。
- 该段是一个只读段，用于防止程序被意外修改
- 该段是可共享的，因此对于[文本编辑器](https://www.zhihu.com/search?q=文本编辑器&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})等频繁执行的程序，内存中只需要一个副本



---

### 3 I/O多路复用

I/O多路复用是一种基于事件驱动的编程模型，允许一个进程同时监听多个输入和输出流，并在可用时对其进行处理，提高系统的性能和吞吐量。它是通过将多个I/O操作集中管理，从而降低了系统的开销和资源消耗。

在传统的阻塞IO模型中，每个IO操作都会导致进程阻塞，直到数据就绪才能进行下一步操作。而在I/O多路复用模型中，通过使用专门的系统调用（如select、poll、epoll），可以同时监视多个流的状态，当流中有数据可读或可写时，系统会通知应用程序进行相应的处理，而不需要将进程阻塞在等待数据上。

I/O多路复用的优点主要体现在以下几个方面：

1. 高效利用资源：通过一次注册多个流，只需要一个线程或进程就可以同时处理多个I/O操作，从而减少了线程或进程的数量和开销，提高了系统的资源利用率。
2. 提高响应速度：当有多个流需要进行I/O操作时，可以同时监听它们的状态，一旦有流有数据可读或可写，就可以立即进行相应的处理，避免了阻塞和等待的过程，提高了系统的响应速度。
3. 简化编程模型：使用I/O多路复用模型可以将多个流的事件集中处理，避免了多线程或多进程之间的同步和通信问题，使得编程更加简单和清晰。

常用的I/O多路复用机制有以下几种：

1. select：是一种较为简单的多路复用机制，通过将要监听的文件描述符放入一个集合中，然后调用select函数进行监听，一旦有事件就绪，返回就会返回可读或可写的文件描述符集合，然后进行相应的处理。
2. poll：与select类似，也是一种多路复用机制，但相较于select，poll使用链表来存储文件描述符，这样不会受到文件描述符数量限制的影响。
3. epoll：是Linux系统提供的一种高性能的多路复用机制。它使用红黑树和时间轮的数据结构来存储和管理文件描述符，可以支持大规模的连接并发，具有高效的触发方式和低延迟。

总而言之，I/O多路复用技术可以提高系统的性能和效率，简化程序设计，适用于需要同时处理多个输入和输出流的场景。但是在具体的实现中，需要根据具体的应用场景和系统环境选择合适的机制。



---

### 4 堆和栈的区别

堆和栈是计算机内存中两种不同的内存分配方式，主要用于管理变量和数据的存储。

1. **分配方式：**
   - 栈：栈是由编译器自动管理的，用于存储局部变量和函数调用的相关信息。在函数调用时，会自动在栈上分配内存。栈的分配和释放是自动进行的，遵循"后进先出"的原则。
   - 堆：堆是程序员手动分配和释放的，用于存储动态内存分配的数据。程序员需要显式地申请内存，并在不使用时显示地释放内存。
2. **管理方式：**
   - 栈：栈是一种高效的内存管理方式，由于它是按照固定的顺序分配和释放内存，管理起来非常简单。栈的分配和释放速度快，开销低。
   - 堆：堆的管理相对复杂，需要程序员手动控制内存的分配和释放。由于堆是动态分配的，可能会产生内存碎片，造成内存的浪费。堆的分配和释放速度相对较慢。
3. **内存空间：**
   - 栈：栈的大小是有限的，一般在编译时就已经确定。栈中存储的变量通常是局部变量和函数调用的相关信息。栈的大小由操作系统决定，过多的栈帧可能导致栈溢出（stack overflow）。
   - 堆：堆的大小一般比栈大得多，并且不受操作系统的限制，大小由程序员根据需要进行动态分配。堆中存储的数据多为动态分配的对象和全局变量。
4. **生命周期：**
   - 栈：栈的生命周期由变量的作用域决定，在变量超出作用域后，栈自动释放变量的内存。
   - 堆：堆的生命周期由程序员手动控制，需要显式地申请内存和释放内存。在不再使用堆中的数据时，程序员需要手动释放内存，否则可能会造成内存泄漏。

总结：栈用于管理局部变量和函数调用相关信息，由编译器自动管理；堆用于动态分配内存，需要程序员手动控制分配和释放。栈的分配速度快，开销低，但大小有限；堆的分配速度相对慢，大小较大，需手动管理。

> 在Windows中，栈的默认大小为1MB；在Unix/Linux系统中，栈的默认大小为8MB



---

### 5 虚拟地址空间是什么

##### 概述

虚拟地址空间是操作系统为每个进程分配的一块内存区域，它提供了一个抽象的视图，让每个进程认为自己独占了整个系统的物理内存。虚拟地址空间的大小是固定的，但不同操作系统的虚拟地址空间大小可能有所不同。

##### 组成

虚拟地址空间通常被划分为几个不同的区域，包括：

1. 代码区：也称为文本区，存放程序的可执行代码，通常是只读的。
2. 数据区：存放程序的全局变量、静态变量和常量数据。
3. 堆区：由程序员动态分配和释放的内存，用于存放程序运行时产生的可变大小的数据。
4. 栈区：用于存放函数调用过程中的局部变量、参数和函数调用的上下文信息。
5. 共享库区：存放系统共享库的代码和数据，不同进程可以共享同一份共享库的虚拟内存。
6. 内核区：用于存放操作系统的内核代码和数据，通常不允许进程直接访问。

##### 作用

1. 内存隔离：每个进程都拥有独立的虚拟地址空间，进程间的内存互不干扰，从而确保数据的安全性和隐私性。
2. 内存管理：操作系统根据需要，动态地为每个进程分配和回收内存。虚拟地址空间使得程序开发人员不需要关注具体物理内存的分配和回收，只需关心虚拟地址的使用。
3. 内存映射：虚拟地址空间允许将磁盘文件映射到内存中，通过读写内存，实现对文件的读写操作。这样可以提高文件操作的效率，简化程序的编写。
4. 内存保护：虚拟地址空间允许操作系统对每个进程的内存进行保护，防止进程越界访问其他进程的内存或者操作系统内核的内存。如果进程发生越界访问，操作系统会立即终止该进程，防止其对系统产生不良影响。
5. 虚拟内存：虚拟地址空间还可以实现虚拟内存技术，将内存和磁盘空间结合起来，允许操作系统将进程暂时不用的数据或代码保存到磁盘，并在需要时再恢复到内存中。这样可以有效地扩展可用内存大小，增加系统的性能和稳定性。



---

### 6 进程间通信方式有哪些？

#### **什么是进程通信**

进程通信（ InterProcess Communication，IPC）就是指**进程之间的信息交换**。

#### **进程通信的背景**

（1）进程在操作系统内核当中都是独立的进程控制块（PCB），也就是一个一个的struct_task{...}结构体对象

![image-20231030205450223](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231030205450223.png)



（2）每个进程都有自己独立的进程虚拟地址空间，也就是说，进程之间的数据是独立的，从而导致进程的独立性

![image-20231030212416200](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231030212416200.png)

（3）要想在进程与进程间建立联系，需要通过内核，在内核中开辟一块缓冲区，两个进程的信息在缓冲区中进行交换或者传递。

![image-20231030205756719](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231030205756719.png)



#### **进程通信的方式**

##### 管道 -- pipe

管道是一种半双工的通信方式，数据只能单向流动

- **管道的种类：**

  - **匿名管道**：只能在具有亲缘关系的进程间使用（进程的亲缘关系通常是指父子进程关系）

  - **命名管道**：允许无亲缘关系进程间的通信。

###### 匿名管道

- **特点**

  - 只能用于具有共同祖先的进程（具有亲缘关系的进程）之间进行通信；通常，一个管道由一个进程创建，然后该进程调用fork()，此后父子进程之间就可以应用该管道。

  - 一般而言，进程退出，管道释放，所以管道的生命周期跟随进程。

  - 一般而言，内核会对管道操作进行同步与互斥

  - 管道是半双工的，数据只能向一个方向流动；需要双方通信时，需要建立起两个管道。

![image-20231030220106938](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231030220106938.png)

- **函数原型**

  ```c++
  #include <unistd.h>
  int pipe(int fd[2]);
  ```

  - 函数
    - pipe函数的成功调用会创建出一个匿名管道，对应在内核中会有一个内核缓冲区
    - 此函数只是创建了管道，要想从管道中读取数据或者向管道中写入数据，需要使用read()和write()函数来完成。
    - 当管道通信结束后，需要使用close()函数关闭管道的读写端。
  - 输入
    - fd[0] 表示管道的读端
    - fd[1]表示管道的写端
    - 都是文件描述符
    - fd参数是输出型参数，不需要程序员赋值，由pipe函数在调用时赋值
  - 返回值
    - 创建成功返回0，创建失败返回-1
    - 并且设置了适当的错误返回信息。

- **函数使用**

```c++
#include <stdio.h>
#include <unistd.h>
#include <string.h>

#define MAXSIZE 100

int main()
{
    int fd[2], pid, line;
    char message[MAXSIZE];
    
    /*创建管道*/
    if(pipe(fd) == -1)
    {
        perror("create pipe failed!");
        return 1;
    }
    /*创建新进程*/
    else if((pid = vfork()) < 0)
    {
        // vfork() : 创建子进程，并先执行子进程，在执行父进程
        perror("not create a new process!");
        return 1;
    }
    /*子进程*/
    else if(pid == 0)
    {
        close(fd[0]);
        printf("child process SEND message!\n");
        write(fd[1], "Hello Linux!",12); /*向文件中写入数据*/ 
        _exit(0);
    }
    /*父进程*/
    else if(pid > 0)
    {
        close(fd[1]);
        printf("parent process RECEIVE message is:\n");
        line = read(fd[0], message, MAXSIZE); /*读取消息，返回消息长度*/
        write(STDOUT_FILENO,message,line); /*将消息写入终端*/
        printf("\n");
        wait(NULL);
        _exit(0);
    }
    return 0;
}


```

输出

![image-20231030215615047](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231030215615047.png)

###### 命名管道

- **函数原型**

  ```shell
  $ mkfifo filename
  ```

  or

  ```c++
  #include <sys/tyoes.h>
  #include <sys/stat.h>
  int mkfifo(const char* pathname, mode_t mode);
  ```

  - pathname是一个文件的路径名，是创建的一个命名管道的文件名；
  - mode是指文件的权限

- **函数使用**

```c++
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdlib.h>
// 文件路径
#define FIFO "/root/process/hello"

int main()
{
    int fd;
    int pid;
    char r_msg[BUFSIZ];
    if((pid = mkfifo(FIFO,0777)) == -1) /*创建命名管道*/
    {
		perror("create fifo channel failed!");
		return 1;
    }
    else
    	printf("create success!\n");
    
    fd = open(FIFO, O_RDWR);  /*打开命名管道*/
    if(fd == -1)
    {
        perror("cannot open the FIFO");
        return 1;
    }
    if(write(fd,"hello world", 12) == -1)  /*写入消息*/
    {
        perror("write data error!");
        return 1;
    }
    else
    	printf("write data success!\n");
    if(read(fd, r_msg, BUFSIZ) == -1)  /*读取消息*/
    {
        perror("read error!");
        return 1;
    }
    else
    	printf("the receive data is:  %s\n",r_msg);
    
    close(fd);   /*关闭文件*/
    
    return 0;
}


```

输出

![image-20231030220736834](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231030220736834.png)



- mkfifo()进行数据传递的过程

  - 用mkfifo()函数创建一个命名管道，命名管道文件路径为/root/process/hello

  - 调用open()函数打开该命名管道文件，以读写的方式打开。

  - 调用write()函数向文件写入信息"hello world", 同时调用read()函数读取该文件，输出到终端。

  - 调用close()函数关闭打开的命名管道文件。



****



##### 消息队列 -- msg queue

- **概述**

管道的缺点就是通信效率低，且只能传输无格式的字节流。

对于消息队列而言，如下图所示，进程A如果想要给进程B发送消息，只需要把数据放入到消息队列中，进程B在需要的时候自行去消息队列中读取数据即可，反过来同理。

<img src="C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231031193241055.png" alt="image-20231031193241055" style="zoom:50%;" />

**消息队列的本质就是存放在内存中的消息的链表，而消息本质上是用户自定义的数据结构**。

一个消息队列由一个标识符（即队列ID）来标识。

- **函数原型**

```c++
#include <sys/msg.h> // 消息队列相关函数及数据结构头文件


int msgget(key_t key, int msg_flg); // 创建消息队列，key值唯一标识该消息队列
/**
 * param:
 	** key: 某个消息队列的名字（关键字），key值的获取既可以通过宏定义key值，也可以通过ftok函数生成key值
 	** msgflg: 由九个权限标志构成
 		*** IPC_CREAT:如果消息队列对象不存在，则创建，否则进行打开操作
 		*** IPC_EXCL:如果消息队列对象不存在则创建，否则产生一个错误并返回
 * return:
 	** 成功则返回一个非负整数，表示该消息队列的标识码
 	** 失败则返回“-1”
*/
int msgrcv(int msg_id, void *msg_ptr, size_t msg_sz, long int msg_type, int msg_flg);// 接收消息
/**
 * param:
 	** msg_id: 由masgget()函数返回的消息队列标识码
 	** msg_ptr: 指针指向准备接收的消息（指的是结构体中内容text的大小，不是结构体本身的大小）
 	** msg_sz: msg_ptr指向的消息的长度
 	** msg_type: 可以实现接受优先级的简单形式，
 		*** msg_type = 0 : 返回队列的第一条消息
 		*** msg_type > 0 : 返回队列第一条类型等于msg_type的消息
 		*** msg_type < 0 : 返回队列第一条类型小于等于msgtype绝对值的消息
 	** msg_flg: 控制着队列没有相应类型的消息可以接收时将要发生的事情
 		*** msg_flg = IPC_NOWAIT表示队列没有可读消息不等待，返回ENOMSG错误
 		*** msg_flg = MSG_NOERROR表示消息大小超过msg_sz时被截断
 		*** msg_flg = MSC_EXCEPT表示，当mssg_type>0时，返回队列第一条不等于msg_type的消息
 			
 * return:
 	** 成功返回实际放到接收缓冲区里的字符个数
 	** 失败返回-1
*/


int msgsnd(int msg_id, const void *msg_ptr, size_t msg_sz, int msg_flg);// 发送消息
/**
 * param:
 	** msg_id: 由masgget()函数返回的消息队列标识码
 	** msg_ptr: 指针指向准备发送的消息
 	** msg_sz: msg_ptr指向的消息的长度，（指的是结构体中内容text的大小，不是结构体本身的大小）
 	** msg_flg: 控制着当前消息队列满或到达系统上限时将要发生的事情
 		*** msg_flg = IPC_NOWAIT表示队列满的时候不等待，返回EAGAIN错误
 * return:
 	** 成功返回0
 	** 失败返回-1
*/


int msgctl(int msg_id, int cmd, struct msgid_ds *buf);// 控制消息队列函数
/**
 * param:
 	** msg_id: 由masgget()函数返回的消息队列标识码
 	** cmd: 将要采取的动作
 		*** IPC_STAT: 把msgid_ds结构中的数据设置为消息队列的当前关联值
 		*** IPC_SET: 在进程有足够权限的前提下，把消息队列的当前关联值设置为msgid_ds数据结构中给定的值
 		*** IPC_RMID: 删除消息队列（若选择删除，则第三个参数为NULL）
 	*
 * return:
 	** 成功返回0
 	** 失败返回-1
*/
```

- **函数使用**

server.c

```c
#include<stdio.h>
#include<stdlib.h>
#include<sys/stat.h>
#include<time.h>

// 用于创建一个唯一的key
#define MSG_FILE "/etc/passwd"

// 消息结构
struct msg_form {
    long mtype;
    char mtext[256];
};

int main()
{
    int msqid;
    key_t key;
    struct msg_form msg;
  
   // 获取key值
   if((key = ftok(MSG_FILE,'z')) < 0)
   {
       perror("ftok error");
       exit(1);
    }

  // 打印key值
    printf("Message Queue - Server key is: %d.\n", key);

   // 创建消息队列
    if ((msqid = msgget(key, IPC_CREAT|0777)) == -1)
    {
       perror("msgget error");
       exit(1);
    }
 
    // 打印消息队列ID及进程ID
    printf("My msqid is: %d.\n", msqid);
    printf("My pid is: %d.\n", getpid());

    // 循环读取消息
    for(;;) 
    {
        msgrcv(msqid, &msg, sizeof(msg.mtext), 888, 0);// 返回类型为888的第一个消息
        printf("Server: receive msg.mtext is: %s.\n", msg.mtext);
        printf("Server: receive msg.mtype is: %d.\n", msg.mtype);

        msg.mtype = 999; // 客户端接收的消息类型
        sprintf(msg.mtext, "hello, I'm server %d", getpid());
        msgsnd(msqid, &msg, sizeof(msg.mtext), 0);
   }
    
   if (msgctl(msgid, IPC_RMID, 0) == -1) { # 删除消息队列，如果删除失败执行if语句并退出
        fprintf(stderr, “msgctl(IPC_RMID) failed\n”);
        exit(EXIT_FAILURE);
   }
   exit(EXIT_SUCCESS);
    
   return 0;
 }
```

client.c

```c
#include<stdio.h>
#include<stdlib.h>
#include<sys/stat.h>
#include<time.h>
// 用于创建一个唯一的key
#define MSG_FILE "/etc/passwd"

// 消息结构
struct msg_form {
    long mtype;
    char mtext[256];
};

int main()
{
    int msqid;
    key_t key;
    struct msg_form msg;

    // 获取key值
    if ((key = ftok(MSG_FILE, 'z')) < 0) 
    {
        perror("ftok error");
        exit(1);
    }

    // 打印key值
    printf("Message Queue - Client key is: %d.\n", key);

    // 打开消息队列
    if ((msqid = msgget(key, IPC_CREAT|0777)) == -1) 
    {
        perror("msgget error");
        exit(1);
    }

    // 打印消息队列ID及进程ID
    printf("My msqid is: %d.\n", msqid);
    printf("My pid is: %d.\n", getpid());

    // 添加消息，类型为888
    msg.mtype = 888;
    sprintf(msg.mtext, "hello, I'm client %d", getpid());
    msgsnd(msqid, &msg, sizeof(msg.mtext), 0);

    // 读取类型为999的消息
    msgrcv(msqid, &msg, sizeof(msg.mtext), 999, 0);
    printf("Client: receive msg.mtext is: %s.\n", msg.mtext);
    printf("Client: receive msg.mtype is: %d.\n", msg.mtype);
    return 0;
}
```



- **消息队列和管道的对比**

1.匿名管道是跟随进程的，消息队列是跟随内核的，也就是说进程结束之后，匿名管道就死了，但是消息队列还会存在（除非显示调用函数销毁）

2.管道是文件，存放在磁盘上，访问速度慢，消息队列是数据结构，存放在内存，访问速度快

3.管道是数据流式存取，消息队列是数据块式存取



---



##### 共享内存 -- shared memory

共享内存（Shared Memory）,指的是两个或多个进程共享一个给定的存储区

- **特点**

  - 共享内存是最快的一种 IPC，因为进程是直接对内存进行存取。

  - 因为多个进程可以同时操作，所以需要进行同步。

  - 信号量+共享内存通常结合在一起使用，信号量用来同步对共享内存的访问。

- **函数原型**

```c
#include <stdio.h>
int shmget(key_t key, size_t size, int flag);
/**
 * 创建或获取一个共享内存
 * param: 
 	* key: 进程间通信关键字，ftok()的返回值
 	* size: 该共享存储段的长度（字节）
 	* flag: 表示函数的行为及共享内存的权限
 		** IPC_CREAT: 如果不存在则创建
 		** IPC_EXCL: 如果已经存在则返回错误
 * return:
 	* 成功返回共享内存ID
 	* 失败返回-1
*/

void shmat(int shm_id, const void *addr, int flag);
/**
 * 连接共享内存到当前进程的地址空间
 * param: 
 	* shm_id: 共享内存标识符，shmget()的返回值
 	* addr: 共享内存映射地址，如果设置为NULL则由系统自动指定
 	* flag: 共享内存段内的访问权限和映射条件（通常为0）
 		** 0: 具有可读可写权限
 		** SHM_RDONLY: 只读
 		** SHM_RND: addr非空时才有效
 * return:
 	* 成功返回共享内存段映射地址（也就是返回指向共享内存的指针）
 	* 失败返回-1
*/

int shmdt(void addr); 
/**
 * 断开与共享内存的连接
 * param: 
 	* addr: 共享内存映射地址
 * return:
 	* 成功返回0
 	* 失败返回-1
*/

int shmctl(int shm_id, int cmd, struct shmid_ds *buf);
/**
 * 控制共享内存的相关信息
 * param: 
 	* shm_id: 共享内存标识符
 	* cmd: 函数功能的控制
 		** IPC_RMID: 删除
 		** IPC_SET: 设置shmid_ds参数，相当于把共享内存原来的属性值替换为buf里的属性值
 		** IPC_STAT: 保存shmid_ds参数，把共享内存原来的属性值被分到buf中
 		** SHM_LOCK: 锁定共享内存段（root）（SHM_LOCK 用于锁定内存，禁止内存交换。并不代表共享内存被锁定后禁止其它进程访问。其真正的意义是：被锁定的内存不允许被交换到虚拟内存中。这样做的优势在于让共享内存一直处于内存中，从而提高程序性能。）
 		** SHM_UNLOCK: 解锁共享内存段
 	* buf: shmid_ds数据类型的地址，可用于存放或修改共享内存的属性
 * return:
 	* 成功返回0
 	* 失败返回-1
*/
```

- **函数使用**

创建两个进程，在 A 进程中创建一个共享内存，并向其写入数据，通过 B 进程从共享内存中读取数据。

写端：

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/shm.h>

#define BUFSZ 512

int main(int argc, char *argv[])
{
    int shmid;
    int ret;
    key_t key;
    char *shmadd;
 
    //创建key值
    key = ftok("../", 2015);
    if(key == -1)
    {
        perror("ftok");
    }
 
    //创建共享内存
    shmid = shmget(key, BUFSZ, IPC_CREAT|0666);    
    if(shmid < 0)
    {
        perror("shmget");
        exit(-1);
    }
 
    //映射
    shmadd = shmat(shmid, NULL, 0);
    if(shmadd < 0)
    {
        perror("shmat");
        _exit(-1);
    }
 
    //拷贝数据至共享内存区
    printf("copy data to shared-memory\n");
    bzero(shmadd, BUFSZ); // 共享内存清空
    strcpy(shmadd, "how are you, lh\n");
 
    return 0;
}

```

读端：

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/shm.h>

#define BUFSZ 512

int main(int argc, char *argv[])
{
    int shmid;
    int ret;
    key_t key;
    char *shmadd;
 
    //创建key值
    key = ftok("../", 2015);
    if(key == -1)
    {
        perror("ftok");
    }
 
    system("ipcs -m"); //查看共享内存
 
    //打开共享内存
    shmid = shmget(key, BUFSZ, IPC_CREAT|0666);
    if(shmid < 0)
    {
        perror("shmget");
        exit(-1);
    }
 
    //映射
    shmadd = shmat(shmid, NULL, 0);
    if(shmadd < 0)
    {
        perror("shmat");
        exit(-1);
    }
 
    //读共享内存区数据
    printf("data = [%s]\n", shmadd);
 
    //分离共享内存和当前进程
    ret = shmdt(shmadd);
    if(ret < 0)
    {
        perror("shmdt");
        exit(1);
    }
    else
    {
        printf("deleted shared-memory\n");
    }
 
    //删除共享内存
    shmctl(shmid, IPC_RMID, NULL);
 
    system("ipcs -m"); //查看共享内存
 
    return 0;
}
```



---



##### 信号量 --Semaphore

- **概述**

​	信号量用于实现进程间的互斥与同步，而不是用于存储进程间通信数据。

- **特点**

  - 信号量用于进程间同步，若要在进程间传递数据需要结合共享内存。

  - 信号量基于操作系统的 PV 操作，程序对信号量的操作都是[原子操作](https://www.zhihu.com/search?q=原子操作&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2931441698})。

  - 每次对信号量的 PV 操作不仅限于对信号量值加 1 或减 1，而且可以加减任意正整数。

  - 支持信号量组。

- **工作原理**

  - P(sv)：如果sv的值大于零，就给它减1；如果它的值为零，就挂起该进程的执行

  - V(sv)：如果有其他进程因等待sv而被挂起，就让它恢复运行，如果没有进程因等待sv而挂起，就给它加1.

  > PV操作均为原子操作

- - **函数原型**

    最简单的信号量是只能取 0 和 1 的变量，这也是信号量最常见的一种形式，叫做二值信号量（Binary Semaphore）。而可以取多个正整数的信号量被称为通用信号量。Linux 下的信号量函数都是在通用的信号量数组上进行操作，而不是在一个单一的二值信号量上进行操作。

    ```C
    #include <stdio.h>
    
    int semget(key_t key, int num_sems, int sem_flags);
    /**
     * 创建或获取一个信号量组
     * param:
     	* key: 进程间通信关键字，ftok()的返回值
     	* num_sems: 指定信号量集中需要的信号量数目，基本是1
     	* flag: 表示信号的创建标志
     		** IPC_CREAT: 如果不存在则创建
     		** IPC_EXCL: 如果已经存在则返回错误
     * return:
     	* 成功返回信号量集ID
     	* 失败返回-1
    */
    
    int semop(int semid, struct sembuf semoparray[], size_t numops);  
    /**
     *  对信号量组进行操作，改变信号量的值
     * param:
     	* semid: semget()返回的信号量集ID
     	* struct sembuf定义如下
     		'''
     		struct sembuf{ 
                short sem_num;   //信号量组中对应的序号，0～sem_nums-1
                short sem_op;   //信号量在一次操作中需要改变的数据，通常是两个数，
                                //一个是-1，即P（等待）操作， 
                                //一个是+1，即V（发送信号）操作。 
                short sem_flg; //通常为SEM_UNDO,使操作系统跟踪信号量， 
                              //并在进程没有释放该信号量而终止时，操作系统释放信号量 
        	}; 
     		'''
     	* nops: 信号量的个数
     * return:
     	* 成功返回0
     	* 失败返回-1
    */
    
    int semctl(int semid, int sem_num, int cmd, ...);
    /**
     * 控制信号量的相关信息
     * param:
     	* semid: semget()返回的信号量集ID
     	* sem_num: 表示当前信号量集中的哪一个信号
     	* cmd: 操作
     		** SETVAL: 用来把信号量初始化为一个已知的值，这个值可以通过设置第四个参数union semun中的val成员设置，其作用是在信号量第一次使用前对它进行设置
     		** IPC_RMID: 用于删除一个已经无需继续使用的信号量标识符，删除操作不需要缺省参数，只需要三个参数
     	* ...: 缺省参数，一般定义为union semun arg;
     		'''
     		union semun
            { 
                int val;  //使用的值
                struct semid_ds *buf;  //IPC_STAT、IPC_SET 使用的缓存区
                unsigned short *arry;  //GETALL,SETALL 使用的数组
                struct seminfo *__buf; // IPC_INFO(Linux特有) 使用的缓存区
            };
     		'''
     * return:
     	* 成功返回0
     	* 失败返回-1
    */
    ```

- **代码实现**

```C
#include<stdio.h>
#include<stdlib.h>
#include<sys/stat.h>
#include<time.h>

// 联合体，用于semctl初始化
union semun
{
    int val; //for SETVAL
    struct semid_ds buf;
    unsigned short *array;
};

// 初始化信号量
int init_sem(int sem_id, int value)
{
    union semun tmp;
    tmp.val = value;
    if(semctl(sem_id, 0, SETVAL, tmp) == -1)
    {
        perror("Init Semaphore Error");
        return -1;
    }
    return 0;
}

// P操作:
//    若信号量值为1，获取资源并将信号量值-1 
//    若信号量值为0，进程挂起等待
int sem_p(int sem_id)
{
    struct sembuf sbuf;
    sbuf.sem_num = 0; /序号/
    sbuf.sem_op = -1; /P操作/
    sbuf.sem_flg = SEM_UNDO;

    if(semop(sem_id, &sbuf, 1) == -1)
    {
        perror("P operation Error");
        return -1;
    }
    return 0;
}

// V操作：
//    释放资源并将信号量值+1
//    如果有进程正在挂起等待，则唤醒它们
int sem_v(int sem_id)
{
    struct sembuf sbuf;
    sbuf.sem_num = 0; /序号/
    sbuf.sem_op = 1;  /V操作/
    sbuf.sem_flg = SEM_UNDO;

    if(semop(sem_id, &sbuf, 1) == -1)
    {
        perror("V operation Error");
        return -1;
    }
    return 0;
}

// 删除信号量集
int del_sem(int sem_id)
{
    union semun tmp;
    if(semctl(sem_id, 0, IPC_RMID, tmp) == -1)
    {
        perror("Delete Semaphore Error");
        return -1;
    }
    return 0;
}

int main()
{
    int sem_id;  // 信号量集ID
    key_t key;  
    pid_t pid;

    // 获取key值
    if((key = ftok(".", 'z')) < 0)
    {
        perror("ftok error");
        exit(1);
    }

    // 创建信号量集，其中只有一个信号量
    if((sem_id = semget(key, 1, IPC_CREAT|0666)) == -1)
    {
        perror("semget error");
        exit(1);
    }

    // 初始化：初值设为0资源被占用
    init_sem(sem_id, 0);

    if((pid = fork()) == -1)
        perror("Fork Error");
    else if(pid == 0) //子进程
    {
        sleep(2);
        printf("Process child: pid=%d\n", getpid());
        sem_v(sem_id);  //释放资源
    }
    else  //父进程//
    {
        sem_p(sem_id);   //等待资源
        printf("Process father: pid=%d\n", getpid());
        sem_v(sem_id);   //释放资源
        del_sem(sem_id); //删除信号量集
    }
    return 0;
}
```



##### 信号 -- signal

- **概述**

​	**信号**是提供处理**异步事件机制**的软件中断。

​	**通过发送指定信号来通知进程某个异步事件的发送，以迫使进程执行信号处理程序。信号处理完毕后，被中断进程将恢复执行**。

​	也就是，A给B发送信号，B收到信号之前执行自己的代码，收到信号后，不管执行到程序的什么位置，都要暂停运行，去处理信号，处理完毕再继续执行。

​	这些事情可以来自系统外部--例如系统产生中断符（通常Ctrl-C），或者来自程序或内核内部的活动，例如进程执行除以零的代码。

​	作为一种进程间通信的基本形式，进程也可以给另一个进程发送信号。

- **信号产生**

  - 按键产生，如：Ctrl+c、Ctrl+z、Ctrl+\
  - 系统调用产生，如：kill、raise、abort
  - 软件条件产生，如：定时器alarm
  - 硬件异常产生，如：非法访问内存(段错误)、除0(浮点数例外)、内存对齐出错(总线错误)
  - 命令产生，如：kill命令

- **进程间使用信号通信**

  - signal()函数注册一个信号捕捉函数

  ```C
  typedef void (*sighandler_t)(int);
  sighandler_t signal(int signum, sighandler_t handler);
  ```

  - **sigaction** 函数修改信号处理动作（通常在Linux用其来注册一个信号的捕捉函数）

  ```C
  int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);  //成功：0；失败：-1，设置errno
  ```

  - **kill** 函数: 给指定进程发送指定信号(不一定杀死)

  ```C
  int kill(pid_t pid, int sig);
  /**
   * pid: 进程号
   * sig: 信号量，当为0时（即空信号），实际不发送任何信号，但照常进行错误检查。因此，可用于检查目标进程是否存在，以及当前进程是否具有向目标发送信号的权限
  */
  ```

  - **raise** 函数：给当前进程发送指定信号(自己给自己发)

  ```C
  //raise(signo) == kill(getpid(), signo);
  int raise(int sig);// 成功：0，失败非0值
  ```

  - **abort** 函数：给自己发送异常终止信号SIGALRM信号。进程收到该信号，默认动作终止。

  ```C
  void abort(void); 该函数无返回
  ```

  - __alarm__函数：设置定时器(闹钟)。在指定seconds后，内核会给当前进程发送SIGALRM信号。进程收到该信号，默认动作终止。

  ```C
  unsigned int alarm(unsigned int seconds); 返回0或剩余的秒数，无失败。
  ```

  - **setalarm** 函数：设置定时器(闹钟)。 可代替alarm函数。精度微秒us，可以实现周期定时。

  ```C
  int setitimer(int which, const struct itimerval *new_value, struct itimerval *old_value);//成功：0；失败：-1，设置errno
  
  /**
   * which：指定定时方式
  	** 自然定时：ITIMER_REAL → 14）SIGLARM 计算自然时间
  	** 虚拟空间计时(用户空间)：ITIMER_VIRTUAL → 26）SIGVTALRM 只计算进程占用cpu的时间
  	** 运行时计时(用户+内核)：ITIMER_PROF → 27）SIGPROF 计算占用cpu及执行系统调用的时间
  
  */
  ```

  

##### Socket套接字编程 -- socket
