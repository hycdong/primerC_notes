## 第3章 字符串、向量和数组
### 3.1 命名空间
 * using namespace::name;声明使用哪种命名空间
 * 如声明了using std::cin;在代码中可以用cin，代替std::cin
 * 一般来说，头文件中不应该包含using声明

### 3.2 标准库类型string
* string的size函数返回的是string::size_type类型的值，它是一个无符号类型的值
  * 可以用`auto len = line.size();`获得该类型
  * 不与int混用（int是带符号值）
* 可以使用关系运算符<,<=,>,>=比较两个string类型，逐一比较字符，且对大小写敏感
  * 若两个string长度不同，且较短的每个字符与较长的对应位置字符相同，则短的较小
  * 若两个string某个位置字符不同，则string对象比较的结果其实是字符比较的结果
* 字符串字面值和sting是不同的类型
* 处理string中的字符可以用for循环进行遍历，用下标进行访问

### 3.3 标准库类型vector
* vector是模板，不是类也不是函数
* 编译器根据模板创建类或函数的过程成为实例化，当使用模板时，需要指出类型，如 `vector<int> ivec;`
* 可以采用列表初始化的方式初始化vector,如`vector<string> v1{"a", "an", "the"};`
* 注意区别列表初始化值和设置容量
  ```
  vector<int> v1(10,1);   //v1有10个元素，每个值都是1
  vector<int> v2{10,2};   //v2有2个元素，值为10和2
  ```
* 在定义vector时通常是不知道其中的元素个数和值的，因此，建议先创建一个空的vector，再通过push_back函数添加元素
* vector不能通过下标的方式添加元素

### 3.4 迭代器

* 容器结构提供迭代器以实现遍历，在C++中使用数组下标并不常见，vector和string都可以用迭代器
* 有迭代器的类型都拥有名为begin和end的成员，begin返回指向第一个元素的迭代器，end成员返回指向容器尾元素的下一个位置的迭代器，也称为尾后迭代器
* 当容器为空时，begin和end都会返回尾后迭代器

  ```
  /* 迭代器使用示例
   * 遍历字符串，依次将每个字符改成大写，处理完所有字符或遇到空白时停止
   */
  string s = "haha";
  for (auto it = s.begin(); it != s.end() && !isspace(*it); ++it)
  {
    *it = toupper(*it);
  }
  ```
* 使用解引用符号得到迭代器所指的元素，如上例中for循环中的语句
* 迭代器的箭头运算符的含义
  * 下例的it是string对象的迭代器
  `(*it).empty()` 等价于 `it->empty()`
* 注意， 若向vector中添加对象同时使用迭代器会使迭代器失效，因此在使用迭代器的同时不要向迭代器所属的容器添加元素
* 迭代器运算
  * 递增运算符令迭代器每次移动一个元素
  * vector和string的迭代器还支持更多运算
    * 迭代器可以与整数相加或者相减
    * 迭代器之间可以使用关系运算符进行比较，若迭代器a指向的容器位置在迭代器b所指的位置之前，则表示前者小于后者
    * 若两个迭代器相减，得到的是它们的距离，距离指右侧迭代器向前移动多少位置就能追上左侧的迭代器，距离的类型是difference_type的带符号整数

### 3.5 数组

* C++中的数组含义与C中类似，大小固定不能动态添加
* 访问数据元素可以使用下标（size_t类型），可以使用指针，C++提供了begin和end函数

  ```
  /*找到array中第一个负数*/
  int *pbeg = begin(array), *pend = end(array);
  while( pbeg != pend && *pbeg >= 0)
    ++pbeg;
  ```
* 数组的两个特点：
  * 不允许copy数组
  * 在使用时通常会将数组转换成指针


* 尽量不要在C++中使用C风格字符串，如有混用的情况，可以使用`string.c_str()`函数得到`char*`类型的C风格字符换

* 由于指针和数组很容易出错，因此在C++中尽量使用vertor和迭代器，避免使用内置数组和指针，应该尽量使用string，避免使用C风格的基于数组的字符串

### 3.6 多维数组
* 实际上并没有多维数组，而是数组的数组
