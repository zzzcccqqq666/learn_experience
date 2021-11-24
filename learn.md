1. c++11关键字之 final override
 - final：
	 - 修饰类，禁止继承
	 - 修饰虚函数，禁止重写
 - override
	 - 确保覆盖虚函数时，函数签名正确

2.const iterator和const_iterator
- const修饰stl容器时，遍历的时候要使用const_iterator，而不是iterator

- C++为每种容器类型定义了一种名为const_iterator的类型，该类型只能用于读取容器内的元素，但不能改变其值。

- const iterator是错误的，他指的是iterator本身是const，而非iterator指向的对象，如果iter++，就会报错

3.make_shared
c++11中引入了智能指针，同时还有一个模板函数std::make_shared可以返回一个指定类型的std::shared_ptr，那与shared_ptr的构造函数相比它能给我们代理什么好处呢？

make_shared初始化的优点
- 提高性能
	- 如果使用原始的new表达式分配对象，然后传递给shared_ptr(也就是使用shared_ptr的构造函数)的话，shared_ptr的实现没有办法选择，而只能单独的分配控制块：
`std::shared_ptr<Widget> spw(new Widget);`
很明显，这段代码需要分配内存，但是它实际上要分配两次。每个shared_ptr都指向一个控制块，控制块包含被指向对象的引用计数及其他东西。这个控制块的内存是在std::shared_ptr的构造函数中分配的。因此直接使用new，需要一块内存分配给Widget，还需要一块内存分配给控制块
	- 如果使用std::make_shared
	`auto spw = std::make_shared<Widget>();`
	一次分配就足够了，这是因为std::make_shared申请一个单独的内存块来同时存放Widget对象和控制块。因为值分配了一次，代码只包含一次内存分配的调用，这会加快代码的执行速度。另外，使用std::make_shared消除了一些控制块需要记录的信息，这样潜在的减少了程序的总内存占用。

- 异常安全
`processWidget(std::shared_ptr<Widget>(new Widget),  //潜在的资源泄露 
              computePriority())`
编译器在把源代码翻译到目标代码，在运行期，函数的参数必须在函数调用前被估值。(也就是，一个Widget对象必须被创建在堆上)
编译器可能产生这样顺序的代码：
```
执行“new Widget”
执行computePriority
执行std::shared_ptr的构造函数。
```
 如果这样的代码被产生出来，并且在运行期间，computePriority产生了一个异常，则在第一步动态分配的Widget就会泄漏了，因为它永远不会被存放到在第三步才开始管理它的std::shared_ptr中。

缺点
- 对象的内存可能无法及时回收
make_shared只分配一次内存，这看起来很好，减少了内存分配的开销。问题来了，weak_ptr会保持控制块的生命周期，而因此连带着保持了对象分配的内存，只有最后一个weak_ptr离开作用域时，内存才会被释放。原本强引用减为0时就可以释放的内存，现在变为了强引用，弱引用都减为0时才能释放，以外的延迟了内存释放的时间。这对于内存要求高的场景来说，是一个需要注意的问题。