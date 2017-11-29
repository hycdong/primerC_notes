## 第7章 类
类的特点
* 数据抽象，定义数据成员和函数成员的能力
* 封装，保护类的成员不被随意访问的能力

### 7.1 定义抽象数据类型
#### 类的成员函数/非成员函数
以书店销售记录类为例，该类有isbn函数，用于得到该记录所表示的书籍的isbn
* 类的成员函数是自动inline的
* C++中this是指向class类型非常量版本的常量指针
* 在默认情况下，不能把this绑定到一个常量对象上，即不能在常量对象上调用普通的成员函数
* 在成员函数的参数列表后的const关键字表示this是一个指向常量的指针，这样的 成员函数被称为**常量成员函数**
```
//const的作用如上所述
std::string isbn() const { return bookNo; }
Sales_data& combine(const Sales_data&);
```

class除了成员函数，如得到书籍isbn的函数，合并两条记录的函数，还包括类似于`read`,`print`这类函数，它们是非成员函数，不属于class，但是也与class定义在同一个头文件中

#### 构造函数
* C++的构造函数不能声明成const
* C++11中可以在参数列表后写 =default 来要求编译器生成构造函数
* 第2,3个构造函数中
  * 冒号及后面的部分被称为**构造函数初始值列表**，为新创建的对象的一个或多个成员赋初值
  * 初始值是成员名字的一个列表，每个名字后面紧跟括号括起来的就是成员初始值

```
//C++11 编译器生成默认构造函数
Sales_data() = default;
//定义两个构造函数
Sales_data(const std::string& s) : bookNo(s) { }
Sales_data(const std::string& s, unsigned n, double p) :
			bookNo(s), units_sold(n), revenue(p*n) {}
//在类外部定义构造函数
Sales_data(std::istream &);
```

### 7.2 访问控制与封装
#### public, private, friend
* 使用public和private关键字可以加强类的封装性
* 将函数或类声明为友元(friend)，该函数或类就能访问这个类的非公有成员（友元的声明只能在类的内部），但是友元的声明仅仅指定了访问权限，还需要在友元声明之外专门对函数进行一次声明

### 7.3 类的其他特性
#### mutable关键字
* 有时我们希望修改某个数据成员，即使在一个const成员函数中，可以用mutable关键字，被声明为mutable的成员，任何函数都能够改变它的值
`mutable size_t access_counts;`

#### friend的说明
* 友元关系没有传递性，如A类是B类的友元，B类是C类的友元，A类与C类没有关系
* 若需要为一组重载函数声明友元，需要每个都声明

### 7.5 构造函数再说明
#### 委托构造函数
* C++11中允许我们定义委托构造函数，如下所示

```
class Sales_data
{
  //非委托构造函数
  Sales_data(const std::string& s, unsigned n, double p) :
			bookNo(s), units_sold(n), revenue(p*n) {}
  //委托构造函数
  Sales_data() : Sales_data("", 0, 0) {}
  Sales_data(std::string s) : Sales_data(s, 0, 0) {}
  //其他与7.1中的版本一致
}
```

### 7.6 类的静态成员
* 类的静态成员不属于类的对象，而是共享的，用static关键字声明，例如银行账户实例的利率字段
* 类的静态成员函数不与任何对象绑定，不包含this指针，并且不能定义为const
*
