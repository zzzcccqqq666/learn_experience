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