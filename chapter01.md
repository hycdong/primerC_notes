## 第1章 开始

### 1.2 初识输入输出
输出运算符`<<`接收两个运算对象，左侧是ostream对象，右侧是要打印的值

```
std::cout << "Enter two numbers:" << std::endl;
```
等价于
```
std::cout << "Enter two numbers:";
std::cout << std::endl;
```
`std::endl`是操纵符，写入endl的效果是结束当前行，并将与设备关联的缓冲区中的内容刷到设备中，可以保证到目前为止程序所产生的所有输出都真正写入输出流中。

>注意：
在print调试信息的时候记得写入endl，否则若程序崩溃，输出可能还留在缓冲区中，从而导致关于程序崩溃位置的错误推断。

### 1.4 控制流(while,for,if)
#### 1.4.3 读取数量不定的输入数据
`while(std::cin >> value)`
循环中判断的是`std::cin`是否为文件结束符号，不同的系统不同，Linux为Ctrl+D
