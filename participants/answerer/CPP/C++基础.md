### 说说静态变量什么时候初始化?

对于C语言的全局和静态变量，初始化发生在任何代码执行之前，属于编译器初始化；但是C++标准规定，全局或者静态对象当且仅当对象首次用到的时候才进行构造，这一定程度上也是从性能方面进行考虑

### 说说 static关键字的作用?

定义静态函数或全局变量：当我们同时编译多个文件时，在函数返回类型或全局变量前加上static关键字，函数或全局变量即被定义为静态函数或静态全局变量。静态函数或静态全局变量只能在本源文件中使用。这就是static的隐藏属性。

static 的第二个作用是保持变量内容的持久：在变量前面加上static关键字。初始化的静态变量会在数据段分配内存，未初始化的静态变量会在BSS段分配内存。直到程序结束，静态变量始终会维持前值。只不过全局静态变量和局部静态变量的作用域不一样。

static 的第三个作用是默认初始化为 0：全局变量也具备这一属性，因为全局变量也存储在静态数据区。在静态数据区，内存中所有的字节默认值都是 0x00 。

最后对 static 的三条基本作用做一句话总结。首先 static 的最主要功能是隐藏，其次因为 static 变量存放在静态存储区，所以它具备持久性和默认值0。

在c++中，static关键字可以用于定义类中的静态成员变量：使用静态数据成员，它既可以被当成全局变量那样去存储，但又被隐藏在类的内部。类中的static静态数据成员拥有一块单独的存储区，而不管创建了多少个该类的对象。所有这些对象的静态数据成员都共享这一块静态存储空间。

在c++中，static关键字可以用于定义类中的静态成员函数：与静态成员变量类似，类里面同样可以定义静态成员函数。只需要在函数前加上关键字static即可。如静态成员函数也是类的一部分，而不是对象的一部分。所有这些对象的静态数据成员都共享这一块静态存储空间。

### 为什么静态成员函数不能访问非静态成员?

静态成员函数不属于任何一个对象，因此C++规定静态成员函数没有this指针。既然它没有指向某一对象，也就无法对一个对象中的非静态成员进行访问。

### 静态成员函数与普通成员函数的区别?

静态成员函数没有 this 指针，只能访问静态成员（包括静态成员变量和静态成员函数）。
普通成员函数有 this 指针，可以访问类中的任意成员；而静态成员函数没有 this 指针。

### 说说volatile和mutable?

mutable是为了突破const的限制而设置的。被mutable修饰的变量，将永远处于可变的状态，即使在一个const函数中，甚至结构体变量或者类对象为const，其mutable成员也可以被修改。mutable在类中只能够修饰非静态数据成员。

一个定义为volatile的变量是说这变量可能会被意想不到地改变，这样，编译器每次会从内存里重新读取这个变量的值，而不是从寄存器里读取。特别是多线程编程中，变量的值在内存中可能已经被修改，而编译器优化优先从寄存器里读值，读取的并不是最新值。这就是volatile的作用了。

### 说说volatile的应用?

Volatile主要有三个应用场景：

（1）外围设备的特殊功能寄存器。

（2）在中断服务函数中修改全局变量。

（3）在多线程中修改全局变量。



#### 左值，左值引用，右值，右值引用

1. 从性能上来看，左右值引用没有区别，传参使用左右值引用都可以避免拷贝

2. 右值引用可以直接指向右值，也可以通过`std::move`指向左值（右值引用相当于static_cast <T &&> (lvalue)；而左值引用只能指向左值（const左值引用也能指向右值）

3. 作为函数形参时，右值引用更加灵活。虽然const左值引用也能指向左值与右值，但是左值引用不能修改

#### 右值引用的作用

C++11引用右值引用可以实现**移动语义**与**完美转发**

- 移动语义：避免临时对象的拷贝，为类增加移动构造函数

- 完美转发，就是通过一个函数将参数继续转交给另一个函数进行处理，原参数可能是右值，或者是左值，如果能继续保持参数的原有特征，那么他就是完美的

#### **移动语义的原理**

移动语义为了避免临时对象的拷贝，为类增加移动构造函数以及移动赋值运算符。**它的原理是将自己的指针指向别人的资源，然后将别人的指针修改为nullptr**



#### 多线程编程修改全局变量需要注意什么？

多线程编程中，变量在内存汇总可能已经被修改，而编译器为了性能会尽量减少访存次数，直接从寄存器取值，这有可能导致读取的值是旧值

**解决方法：**

1. 全局变量加关键字volatile

2. 使用原子操作，效率比锁高

3. 使用互斥锁



#### 原子操作

原子操作指的是一系列动作必须一气呵成，不能出现分步完成的操作

原子操作与互斥锁类似，但是它比锁的效率高，这是因为原子操作更加接近底层，它的实现原理是基于**总线加锁**与**缓存加锁**的方式。