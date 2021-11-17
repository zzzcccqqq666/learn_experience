## HashMap的内部实现机制
HashMap的内部实现机制是对数据结构中哈希表（Hash Table）的实现，Hash表又叫散列表。
Hash表是根据关键码Key来访问其对于的值Value的数据结构，它是通一个映射函数吧关键码映射到表中的一个位置来访问该位置的值，从而加快查找的速度。这个映射函数叫做Hash函数，存放的数组叫Hash表。

## Hash的实现
主要是哈希冲突和冲突的解决
计算出map中key的哈希值，在哈希表中找到value的位置，根据key找value

当多个哈希值冲突的时候，在哈希值的同一个位置上使用链表结构来保存多个对象

hashMap方法为的时候是根据key的hashCode值来快速定位，如果HashMap中有亮哥哥以上的元素具有相同的hashCode值，将会导致性能下降

## rehash
在创建hashMap的时候可以设置两个参数
初始化容量：创建hash表时桶的数量
负载因子：map的size/初始化容量

当hash表中的负载因子达到负载极限的时候，hash表会自动成倍的增加容量（桶的数量），并将原有的对象重新分配并加入新的桶内，这称为rehash。这个过程是十分耗性能的，一般建议设置比较大的初始化容量，防止rehash，但是也不能设置的过大，初始化容量过大，浪费空间。

## 如何计算hashmap占用内存的大小？例如unordered_map,unoreded_set

一种粗略的方法：
- float max_load_factor()：返回unordered_map容器的当前最大负载因子。负载因子是容器中元素的数量（其大小）与存储桶数量（bucket_count）之间的比率。
- bucket_count()：存储桶数量

负载因子 * 桶的数量 * （一个指针大小 + 元素大小）

```c++
  double res;
  unsigned n = hashmap.bucket_count();
  float m = hash.max_load_factor();
  if (m > 1.0) {
    res =  n * m;
  }
  else {
    res =  n;
  }
	res *= (4 + element_size)
```
	
