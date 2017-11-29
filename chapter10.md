## 第10章 泛型算法
&emsp;&emsp;从之前的章节中可以看到，顺序容器只定义了很少的操作，例如添加元素，删除元素，访问首尾元素，获得首尾位置的迭代器等  
&emsp;&emsp;但是，其实用户可能需要更多的函数，例如查找特定元素、替换或删除一个特定的值、重排元素顺序等  
&emsp;&emsp;标准库并没有为每个容器都定义函数来实现这些操作，而是定义了一组泛型算法，实现了一些经典算法的公共接口。

### 10.1 概述
&emsp;&emsp;一个很好的例子能够说明泛型算法，例如，我们有一个int的vector，我们希望知道其中是否包含某个特定的值，最直接的方法就是使用标准库函数 `find`，示例如下：
```
int val = 90;
auto result = find(vec.cbegin(), vec.cend(), val);
```
&emsp;&emsp;find函数的前两个参数是迭代器范围，第三个参数是待查找的值，find函数将返回指向第一个等于给定值的元素的迭代器，若查找失败，则返回迭代器end指向的位置  

> 关键概念：**算法不会执行容器的操作**  
&emsp;&emsp;泛型算法只会运行于迭代器上，执行迭代器的操作，即算法不会改变底层容器的大小。需要特殊说明的是，标准库提供一种特殊的迭代器inserter，算法操作这样的迭代器时，迭代器可以完成添加元素的效果，但是算法自身不会做这样的操作。

### 10.2 初识迭代算法
&emsp;&emsp;除了少数例外，多数标准库函数都对一个指定范围内的元素进行操作，该范围被称为“输入范围”。
- 只读算法（迭代器最好用cbegin和cend）
  - find
  - count
  - accumulate (求和范围+求和初值)  
- 写容器元素的算法
  - fill (向输入范围写入元素)
  - fill_n (开始迭代器+计数值+修改的值)
  - copy (原序列的输入范围+目的序列的起始位置)
- 重排元素的算法
  - sort
  - unique 

#### accumulate
&emsp;&emsp;accumulate的第三个参数的类型决定了使用哪个加法运算符，如下例，sum为vec中所有元素的和，str为v中所有字符的拼接。
```
int sum = accumulate(vec.cbegin(), vec.cend(), 0);
string str = accumulate(v.cbegin(), v.cend(), "");
```
#### fill_n与back_inserter
&emsp;&emsp;使用fill_n时并不会进行安全检查，最常见的错误就是对一个空的vector使用fill_n函数，因此，需要我们在向目的迭代器写入数据之前，确定该位置是存在的。  
&emsp;&emsp;一种保证迭代器位置存在的方法是使用**插入迭代器**，我们可以通过插入迭代器向被指向的元素赋值，如back_inserter，它接受一个指向容器的引用，返回一个指向与该容器绑定的插入迭代器。下面例子中，在每步迭代中，fill_n向v末尾插入一个元素，元素的值为0。
```
vector<int> v;
fill_n(v.begin(), 10, 0); //错误：v中不包含10个元素
fill_n(back_inserter(v), 10, 0);  //正确
```

### 10.3 定制操作
&emsp;&emsp;sort排序默认是根据类型的<进行排序，但可能有时我们希望采用不同的规则来进行排序，可以使用`谓词`和`lambda表达式`。

#### 谓词
&emsp;&emsp;依旧以sort函数为例，重载的sort函数除了输入范围之外，还能接受第三个参数，该参数是一个**谓词**，如下例中的isShort函数。
- 谓词是一个可调用的表达式，其返回结果是一个能用作条件的值
- 标准库算法使用的谓词根据参数个数分为一元谓词和二元谓词
- 接受谓词参数的算法对输入序列中的元素调用谓词，因此，元素类型必须能转换成谓词的参数类型。
```
bool isShort(const string &s1, const string &s2){
  return s1.size() < s2.size();
}
//按长度由短到长对words中的单词进行排序
sort(words.begin(), words.end(), isShort);
//有序序列中相等的元素的位置与排序前一致
stable_sort(words.begin(), words.end(), isShort);
```

#### lambda表达式
&emsp;&emsp;谓词有参数个数的限制，有时我们需要更多参数，或者谓词不能满足我们的要求。事实上，我们可以向算法传递任何**可调用对象**：函数、函数指针、重载函数调用运算符的类`见14.8`以及lambda表达式。  
&emsp;&emsp;一个lambda表达式表达了一个可调用的代码单元，可以理解为一个**未命名的内联函数**。基本语法为：  
`[capture list] (parameter list) -> return type {function body}`   
- 捕获列表是一个lambda表达式所在函数中定义的局部变量的列表
- 参数列表，返回值和函数主体都和普通的函数一样  

```
//用lambda表达式重写上述排序函数
stable_sort(words.begin(), 
            words.end(), 
            [](const string &s1, const string &s2){
              return s1.size() < s2.size();
            });
```
&emsp;&emsp;在按长度排序之后，我们还想知道超过某个长度的单词数量有多少，可以使用find_if函数，查找第一个长度大于等于sz的元素，
```
//lambda表达式捕获列表不为空的示例
auto wc = find_if(words.begin(), 
                  words.end(),
                  [size](const string &str){
                      return str.size() >= size;
                  });
auto count = words.end() - wc;
```
&emsp;&emsp;除了如上所述的显式捕获外，还可以让编译器根据lambda表达式的函数体中的代码推断我们需要什么变量。为了指示编译器推断捕获列表，需要在捕获列表中写一个‘&’或‘=’。
- ‘&’告诉编译器采用引用捕获的方式
- '='告诉编译器采用值捕获方式
- 若混合使用，则捕获列表的第一个参数必须是'&'或'='

```
void printWords(vector<string> words,
                vector<string>::size_type sz,
                std::ostream &os = cout,
                char c = ' '){
  //os是隐式引用
  for_each(words.begin(), words.end(),
          [&, c](const string &s){
            os << s << c;
          }
  //c是隐式引用
  for_each(words.begin(), words.end(),
          [=, &os](const string &s){
            os << s << c;
          }
  );
}
```
&emsp;&emsp;默认情况下，对于一个值被拷贝的变量，lambda表达式不会改变它的值。如果我们希望改变一个被捕获的变量的值，就必须在参数列表首加上关键字`mutable`，**可变lambda**示例如下：
```
size_t v1 = 42;
auto f1 = [v1]  () mutable { return ++v1; };
auto f2 = [&v1] () mutable { return ++v1; };
v1 = 0;
auto j1 = f1();   //j1 = 43
auto j2 = f2();   //j2 = 1
```

#### bind

&emsp;&emsp;对于那种只在一两个地方使用的简单操作，lambda表达式是不错的选择，但是若经常使用且语句复杂，则还是使用函数更好。  
&emsp;&emsp;若lambda表达式的捕获列表为空，可以很容易的用函数代替它，如之前的isShort函数，但若有捕获列表，则不那么容易。例如find_if，如果我们把lambda表达式改成函数check_size，如下所示，但是find_if接受一个一元谓词，只能有一个参数，因此要用check_size代替lambda表达式需要解决参数传递的问题。
```
bool check_size(const string &str, string::size_type size){
  return str.size() >= size;
}
```
&emsp;&emsp;使用C++标准库提供的bind函数能够解决这个问题。bind函数是函数适配器，它接收一个可调用对象，生成一个新的可调用对象来“适应”原对象的参数列表，调用形式如下：  
&emsp;&emsp;`auto newCallable = bind(callable, arg_list);`  
&emsp;&emsp;当我们调用newCallable时，newCallable会调用callable，并传递给它的arg_list中的参数。arg_list可能包含形如_n的名字，这些参数是**占位符**，表示newCallable的参数，它们占据了传递给newCallable的参数的“位置”，数值n表示生成的可调用对象中参数的位置：_1表示newCallable的第一个参数，占位符定义在std::placeholder命名空间中。  
&emsp;&emsp;使用bind的示例如下，bind生成一个可调用对象，将check_size的第二个参数绑定到size的值上。
```
auto wc = find_if(words.begin(),
                  words.end(),
                  bind(check_size, _1, size));
```
&emsp;&emsp;使用bind还能**重排参数顺序**，如下例，在第一个调用中，当sort需要比较两个元素A和B时，它会调用isShort(A,B)，在第二个调用中，bind将参数换了，好像调用了isShort(B,A)
```
sort(words.cbegin(), words.cend(), isShort);
sort(words.cbegin(), words.cend(), bind(isShort, _2, _1));
```
