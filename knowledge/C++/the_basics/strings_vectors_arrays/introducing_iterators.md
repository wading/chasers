# C3.4 迭代器介绍
我们已经知道可以使用下标运算符来访问`string`对象的字符串或`vector`对象的元素，还有另外一种更通用的机制也可以实现同样的目的，这就是**迭代器（iterator）**。

类似于指针类型，迭代器也提供了对对象的间接访问。

就迭代器而言，其对象是容器中的元素或者`string`对象中的字符。使用迭代器可以访问某个元素，迭代器也能从一个元素移动到另外一个元素。使用迭代器有有效和无效之分，这一点和指针差不多。有效迭代器或者指向某个元素，或者指向容器中尾元素的下一个位置；其他所有情况都属于无效。

## 使用迭代器
和指针不一样的是，获取迭代器不是使用取地址符，有迭代器的类型同时返回迭代器的成员。这些类型都拥有名为**begin**和**end**的成员，其中`begin`成员负责返回指向第一个元素（或第一个字符）的迭代器。
```cpp
auto b = v.begin(),e = v.end();
```

`end`成员负责返回指向容器（或`string`对象）“尾元素的下一个位置（one past the end）”的迭代器，也就是说，该迭代器指示的是容器的一个本不存在的“**尾后（off the end）**元素。这样的迭代器没有什么实际含义，仅是一个标记而已，表示我们已经处理完了容器中的所有元素。

`end`成员返回的迭代器常被称作**尾后迭代器（off-the-end iterator）**或者简称为尾迭代器（end iterator）。特殊情况下如果容器为空，则`begin`和`end`返回的是同一个迭代器。

### 迭代器运算符

|运算符|说明|
|---|---|
|`*iter`|返回迭代器`iter`所指元素的引用|
|`iter->mem`|解引用`iter`并获取该元素的名为`mem`的成员，等价于`(*iter).mem`|
|`++iter`|令`iter`指示容器的下一个元素|
|`--iter`|令`iter`指示容器的上一个元素|
|`iter1==iter2`,`iter1!=iter2`|判断两个迭代器是否相等（不相等），如果两个迭代器指示的是同一个元素或者它们是同一容器的尾后迭代器，则相等；反之，不相等|

和指针类似，也能通过解引用迭代器来获取它所指示的元素，执行解引用的迭代器必须合法并确实指示着某个元素。试图解引用一个非法迭代器或者尾后迭代器都是违背定义的行为。

### 将迭代器从一个元素移动到另一个元素
迭代器使用递增（`++`）运算符来从一个元素移动到下一个元素。从逻辑上来说，迭代器的递增和整数的递增类似，整数的递增是在整数值上“加1”，迭代器的递增则是将迭代器“向前移动一个位置”。

### 迭代器类型
那些拥有迭代器的标准库类型使用`iterator`和`const_iterator`来表示迭代器的类型：
```cpp
vector<int>::iterator it;
string::iterator it2;

vector<int>::const_iterator it2;        //it3只能读元素，不能写元素
string::const_iterator it4;             //it4只能读字符，不能写字符
```

### begin和end运算符
`begin`和`end`返回的具体类型由对象是否是常量决定，如果对象是常量，`begin`和`end`返回`const_iterator`；如果对象不是常量，返回`iterator`:
```cpp
vector<int> v;
const vector<int> cv;
auto it1 = v.begin();
auto it2 = cv.begin();
```

C++ 11新标准引入了两个新函数，分别是`cbegin`和`cend`，无论`vector`对象本身是否是常量，返回值都是`const_iterator`。

### 结合解引用和成员访问操作
解引用迭代器可获得迭代器所指的对象，如果该对象的类型恰好是类，就有可能希望进一步访问它的成员。

例如，对于一个由字符串组成的`vector`对象来说，要想检查其元素是否为空，令`it`是该`vector`对象的迭代器，只需检查`it`所指字符串是否为空就可以了，其代码如下所示：
```cpp
(*it).empty();
```

为了简化上述表达式，C++语言定义了**箭头运算符（->）**箭头运算符把解引用和成员访问两个操作结合在一起，也就是说，`it->mem`和`(*it).mem`表达的意思相同。

## 迭代器运算
迭代器的递增运算令迭代器每次移动一个元素，所有标准容器都有支持递增运算的迭代器。类似的，也能用`==`和`!=`对任意标准类型的两个有效迭代器进行比较。

|表达式|说明|
|---|---|
|`iter + n`|迭代器加上一个整数值仍是一个迭代器，迭代器指示的新位置与原来相比向前移动了若干个元素。结果迭代器或者指示容器内的一个元素，或者指示容器尾元素的下一个位置|
|`iter - n`|迭代器减去一个整数值仍得到一个迭代器，迭代器指示的新位置与原来相比向后移动了若干个元素。结果迭代器或者指示容器内的一个元素，或者指示容器尾元素的下一个位置|
|`iter1 += n`|迭代器加法的复合赋值语句，将`iter1`加`n`的结果赋给`iter1`|
|`iter1 - iter2`|两个迭代器相减的结果是它们之间的距离，也就是说，将运算符右侧的迭代器向前移动差值个元素后将得到左侧的迭代器。参与运算的两个迭代器必须指向的是同一个容器内的元素或者尾元素的下一个元素|
|`>`,`>=`,`<`,`<=`|迭代器的关系运算符，如果某迭代器指向的容器位置在另一个迭代器所指位置之前，则说位置小于后者。参与运算的两个迭代器必须指向的是同一个容器中的元素或者尾元素的下一个位置|

