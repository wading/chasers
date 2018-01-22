# C3.2 标准库类型string
标准库类型`string`表示可变长的字符序列，使用`string`类型必须首先包含`string`头文件。作为标准库的一部分，`string`定义在命名空间`std`中。接下来的示例都假定已经包含了下述代码：
```cpp
#include <string>
using std::string
```

本节描述最常用的`string`操作，在[9.5]()节还将介绍另外一些。

## 定义和初始化`string`对象
如何初始化类的对象是由类本身决定的。一个类可以定义很多种初始化对象的方式，只不过这些方式之间必须有所区别：
> - 或者是初始值的数量不同
> - 或者是初始值的类型不同

**初始化string对象的几种方式**：

|表达式|说明|
|---|---|
|`string s1`|默认初始化，s1是一个空串|
|`string s2(s1)`|s2是s1的副本|
|`string s2 = s1`|等价于`s2(s1)`，s2是s1的副本|
|`string s3("value")`|`s3`是字面值"value"的副本，除了字面值最后的那个空字符外|
|`string s3 = "value"`|等价于`s3("value")`，s3是字面值“value”的副本|
|`string s4(n,'c')`|把s4初始化为由连续n个字符c组成的串|

### 直接初始化和拷贝初始化
如果使用一个等号（`=`）初始化一个变量，实际上执行的是**拷贝初始化（copy initialization）**，编译器把等号右侧的初始值拷贝到新创建的对象中去。与之相反，如果不使用等号，则执行的是**直接初始化（direct initialization）**。

## string对象上的操作
一个类除了要规定初始化对象的方式外，还要定义对象上所能执行的操作。

**string的操作**  

|表达式|说明|
|---|---|
|`os << s`|将`s`写到输出流`os`当中，返回`os`|
|`is >> s`|从`is`中读取字符串赋给`s`，字符串以空白分隔，返回`is`|
|`getline(is,s)`|从`is`中读取一行赋给`s`，返回`is`|
|`s.empty()`|`s`为空返回`true`，否则返回`false`|
|`s.size()`|返回`s`中字符的个数|
|`s[n]`|返回`s`中第`n`个字符的引用，位置`n`从`0`计起|
|`s1 + s2`|返回`s1`和`s2`连接后的结果|
|`s1 = s2`|用`s2`的副本替代`s1`中原来的字符|
|`s1 == s2`|如果`s1`和`s2`中所含的字符完全一样，则它们相等；string对象的相等性判断对字母的大小写敏感|
|`s1 != s2`|判断`s1`和`s2`不相等|
|`<`,`<=`,`>`,`>=`|利用字符在字典中的顺序进行比较，且对字母的大小写敏感|

### 读写string对象
```cpp
int main()
{
    string s;
    cin >> s;
    cout << s << endl;
    return 0;
}
```
这段程序首先定义一个名为`s`的空`string`，然后将标准输入的内容读取到`s`中。在执行读取操作时，`string`对象会自动忽略开头的空白（空格符、换行符、制表符等）并从第一个真正的字符开始读取，直到遇见下一处空白为止。

### 读取未知数量的string对象
```cpp
int main()
{
    string word;
    while (cin >> word){
        cout << word << endl;
    }
    return 0;
}
```

该条件负责在读取的时候检测流的情况，如果流有效，也就是说没有遇到文件结束符标记或非法输入，那么执行`while`语句内部的操作。此时，循环体将输出刚刚从标准输入读取到的内容。重复若干次之后，一旦遇到文件结束标记或非法输入循环也就结束了。

### 使用getline读取一整行
有时候我们希望能在最终得到的字符中保留输入时的空白符，这时应该用**getline**函数代替原来的`>>`运算符。`getline`函数的参数是一个输入流和一个`string`对象，函数从给定的输入流中读入内容，直到遇到换行符为止（注意换行符也被读进来了），然后把所读取的内容放入那个`string`对象中去（注意不存换行符）。

`getline`只要一遇到换行符就结束读取操作并返回结果，哪怕输入的一开始就是换行符也是如此。如果输入真的一开始就是换行符，那么所得的结果是个空`string`。

和输入运算符一样，`getline`也会返回它的流参数。因此既然输入运算符能作为判断的条件，我们也能用`getline`的结果作为条件。
```cpp
int main()
{
    string line;
    while (getline(cin,line))
        cout << line << endl;
    return 0;
}
```

因为`line`中不包含换行符，所以我们手动地加上换行操作符。

### string的empty和size操作
顾名思义，**empty**函数根据`string`对象是否为空返回一个对应的布尔值。

```cpp
while (getline(cin,line))
    if (!line.empty())
        cout << line << endl;
```

**size**函数返回`string`对象的长度（即string对象中字符的个数），可以使用`size`函数只输出长度超过80的字符的行：
```cpp
string line;
while (getline(cin,line))
    if (line.size() > 80)
        cout << line << endl;
```

### string::size_type类型
对于`size`函数来说，返回一个`int`或者返回一个`unsigned`似乎都是合情合理的，但其实`size`函数返回的是一个`string::size_type`类型的值，下面就对这种新的类型稍作解释。

`string`类及其他大多数标准库都定义了几种配套类型。这些配套类型体现了标准库类型与机器无关的特性，类型`size_type`即是其中一种。在具体使用的时候，通过作用域操作符来表明名字`size_type`是在类`string`中定义的。

尽管我们不太清楚`string::size_type`类型的细节，但有一点是肯定的：它是一个无符号类型的值而且能足够存放下任何`string`对象的大小。所有用于存放`string`类的`size`函数返回值的变量，都应该是`string::size_type`类型的。

由于`size`函数返回的是一个无符号整数，因此切记，如果在表达式中混用了带符号数和无符号数将可能产生意想不到的结果。例如，假设`n`是一个具有负值的`int`，则表达式`s.size() < n`的判断结果几乎肯定为`true`。这是因为负值`n`会被自动地转换成一个比较大的无符号数。

### 比较string对象
`string`类定义了几种用于比较字符串的运算符。这些运算符逐一比较`string`对象中的字符，并且对大小写敏感，也就是说，在比较时同一字母的大写形式和小写形式是不同的。

相等性运算符（`==`和`！=`）分别检验两个`string`对象相等或不相等，`string`对象相等意味着它们的长度相同且所包含的字符也全都相同。

关系运算符`<`,`<=`,`>`,`>=`分别检验一个`string`对象是否小于、小于等于、大于、大于等于另外一个`string`对象。上述这些运算符都依照（大小写敏感的）字典顺序：
> - 如果两个`string`对象的长度不同，而且较短`string`对象的每个字符都与较长`string`对象对应位置上的字符相同，就说较短`string`对象小于较长`string`对象。
> - 如果两个`string`对象在某些对应位置上不一致，则`string`对象比较的结果其实是`string`对象中第一对相异字符比较的结果。

### 为string对象赋值
一般来说，在设计标准库类型时都力求在易用性上向内置对象类型看齐，因此大多数库类型都支持赋值操作。

```cpp
string str1(10,'c'),str2;
str2 = str1;
```

### 两个string对象相加
两个`string`对象相加得到一个新的`string`对象，其内容是把左侧的运算对象与右侧运算对象串接而成。也就是说，对`string`对象使用加法运算符（`+`）的结果是一个新的`string`对象，它所包含的字符由两部分组成：前半部分是加号左侧`string`对象所含的字符、后半部分是加号右侧`string`对象所含的字符。

另外，复合赋值运算符（`+=`）负责把右侧`string`对象中的内容追加到左侧`string`对象的后面。

### 字面值和string对象相加
因为标准库允许把字符字面值和字符串字面值转换成`string`对象，所以在需要`string`对象的地方就可以使用这两种字面值来替代。

当把`string`对象和字符字面值及字符串字面值混合在一条语句中使用时，必须保证每个加法运算符（`+`）的两侧的运算对象至少有一个是`string`。

## 处理string对象中的字符
使用`cctype`头文件中定义的一组标准库函数处理这部分工作

|函数名|含义|
|---|---|
|`isalnum(c)`|当`c`是字母或数字时为真|
|`isalpha(c)`|当`c`是字母时为真|
|`iscntrl(c)`|当`c`是控制字符时为真|
|`isdigit(c)`|当`c`是数字时为真|
|`isgraph(c)`|当`c`不是空格但可以打印时为真|
|`islower(c)`|当`c`是小写字母时为真|
|`isprint(c)`|当`c`是可打印字符时为真（及`c`是空格或`c`具有可视形式）|
|`ispunct(c)`|当`c`是标点符号时为真（即`c`不是控制字符、数字、字母、可打印空白中的一种）|
|`isspace(c)`|当`c`是空白字符时为真（即`c`是空格、横向制表符、纵向制表符、回车符、换行符、进纸符中的一种）|
|`isupper(c)`|当`c`是大写字母时为真|
|`isxdigit(c)`|当`c`是十六进制数字时为真|
|`tolower(c)`|如果`c`是大写字母，输出对应的小写字母；否侧原样输出`c`|
|`toupper(c)`|如果`c`是小写字母，输出对应的大写字母；否侧原样输出`c`|

### 处理每个字符？使用基于范围的for语句
C++ 11提供了一个语句：**范围for（range for）**。这种语句遍历给定序列中的每个元素并对序列中的每个值执行某种操作，其语法形式是：
```cpp
for (declaration : expression)
    statement
```

其中，`expression`部分是一个对象，用于表示一个序列。`declaration`部分负责定义一个变量，该变量将被用于访问序列中的基础元素。每次迭代，`declaration`部分的变量会被初始化为`expression`部分的下一个元素值。

例如，打印字符序列中的所有字符：
```cpp
string str("some string");
for (auto c : str)
    cout << c << endl;
```

例如，改变字符串中的字符：
```cpp
string str("hello world");
for (auto &c : str)
    c = toupper(c);
cout << str << endl;
```

### 只处理一部分字符？
要想访问`string`对象中的单个字符有两种方式：一种是使用下标，另一种是使用迭代器。

**下标运算符（`[ ]`）** 接收的输入参数是`string:size_type`类型的值，这个参数表示要访问的字符的位置；返回该位置上字符的引用。

`string`对象的下标是从`0`计起。