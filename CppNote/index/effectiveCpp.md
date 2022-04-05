# Effective Cpp Note

## 1. 让自己习惯 C++

### 1.1 视 C++ 为一个语言联邦

- **C** C++ 的基础。
- **Object-Oriented C++** 面向对象。
- **Template C++** 模板编程，拓展了 C++ 的泛型能力。
- **STL** 标准模板库。

### 1.2 使用 `const` `enum` `inline` 而不是 `#define`

即使用编译行为代替预处理行为。

可获得更清晰的符号跟踪。

```Cpp
const double PI = 3.1415926;
const char* const name="LviatYi";

class Class{
    static const int MAX_CAPACITY = 30;// 静态常量声明
};

const int Class::MAX_CAPACITY; //常量定义
```

> 「声明」仅建立了一个标识符。而「定义」赋予标识符空间。
>
> - 常量若只有带赋初值的声明，使用时可能采用立即数替代。
> - 这种量若无声明，取地址行为可能是未定义的。某些编译器（GCC）将报错，某些编译器（MSVC）则会通过编译。

永远不要使用宏函数，即使带了控制优先级以避免注入的括号。

```cpp
#define CALL_F_WITH_MAX(a,b) f((a) > (b) ? (a) : (b))   // NEVER

templete<typename T>
inline void CallFWithMax(const T& a, const T& b){
    f((a) > (b) ? (a) : (b));
}
```

### 1.3 尽可能使用 `const`

`const` 指定「只读」语义约束，借助编译器保障这个约束不被其他编程者违反。

- **其他编程者** 包含经历过某些时空变换的自己。

```Cpp
const typename *p;  // 指向 const 的指针。指向一个只读量。
typename * const p; // const 指针。p 本身只读。

std::vector<int>::const_iterator citer   // 指向 const 的指针。
const std::vector<int>::iterator iter   // const 指针。
```

---

#### 返回 `const` 的函数

返回 `const` 类型的函数可避免对表达式赋值。

```c++
const Number operator*(const Number& lhs, const Number& rhs);
...

Number a,b,c;
...
if( a * b = c ){}   //ERROR
```

---

#### `const` 成员函数

`const` 作用于成员函数是一种只读保证。

- 程序员可以知道哪些函数不会改动成员内容。
- 编译器也可以了解到这一点，因此允许其处理 const 对象。

> 如果两个成员函数只是常量性 (constness) 的不同，可以被 **重载** 。

`const` 修饰对于成员函数有时可能过于专制。这是 **bitwise** 的。

- `bitwise constness` 保证对象内的任何一个 bit 都不会更改。
  - 但这种保证却可能带来预期外的效果：指针。指针指向的内容并不受对象管理。
- `logical constness` 放松管制，允许对象内某些特殊的成员被改动。

`logical constness` 也是受编译器支持的，即使用 `mutable` 。`mutable` 将释放 non-static 成员的 bitwise 约束。

```c++
class CTextBlock{
public:
    std::size_t length() const;
private:
    char* pText;
    mutable std::size_t textLength;
    mutable bool lengthIsValid;
};

std::size_t CTextBlock::length() const{
    if(!lengthIsValid){
        textLength = std::strlen(pText);    // 允许的
        lengthIsValid = true;               // 允许的
    }
    return textLength;
}
```

---

#### 使用 `const_cast` 避免重复

很多时候使用 `const` 对 `non-const` 的重载，可能都包含大量的重复代码。实际上，大部分情况的 `const` 重载，只是多了限定返回类型的 `const` 关键字而已。

这个使用可以使用 `const_cast` 将 `const` 强制转换为 `non-const` ，并复用代码。

```c++
class TextBlock{
public:
    const char& operator[](std::size_t index) const{
        ...
        return text[index];
    }
    char& operator[](std::size_t index){
        return const_cast<char&>(
            static_cast<const TextBlock&>(*this)[index]
        );
    }
};
```

> 首先 `static_cast<const TextBlock&>` 强制转型为 `const` 避免自身调用。  
> 其次 `const_cast<char&>` 强制取消 `const` 。

不要反向实现，即通过 `const` 版本调用 `non-const` 版本。

- `const` 函数保证其对象的逻辑状态 (logical state) 没有改变，而调用的 `non-const` 没有这种保证。

### 1.4 确定对象在被使用前已被初始化

永远初始化对象，包括在任何位置的内置类型。

> 无论你对规则的记忆有多好。

在对象中，应区分 **赋值** (assignments) 与 **初始化** (initializations)。

```c++
class Student{
    string name;
    SchClass schClass;
    int age;

public:
    Student(
        const string& _name
    );
};

Student::Student(
    const string& _name
){
    this->name = _name;          // 这是赋值而非初始化。
    this->schClass = SchClass(); // 这甚至不应称为「赋初值」
    this->age = 0;
}
```

实际上，成员变量的初始化发生在调用默认构造函数之前。当进入构造函数内，其内部字段已经初始化完成（且已经发生一次赋初值行为）。

使用效率更高的 **成员初值列表** (member initialization list) 完成初始化。

```c++
Student::Student(
    const string& _name；
):  name(_name),
    schClass(),
    age(0) {};
```

这将直接调用复制构造函数代替「初始化、赋初值、再赋值」的构造过程。

对象的成员初始化具有固定的顺序，与成员初值列表无关。

- base classes 。
- 依次声明的成员变量。

---

#### 「不同编译单元内定义之 non-local static 对象」的初始化次序

`static` 对象，即所有寿命以构造为开始，以程序结束为结束的对象。包含：

- global 对象
- 定义域 namespace 作用域内的对象。
- 在 classes 内、函数内、在 file 作用域内被声明为 static 的对象。

其中：

- 函数内 static 对象称为 **local static 对象** 。
- 其他为 **non-local static 对象** 。

**编译单元** (translation unit) 指产出单一目标文件 (single obj file) 的源码。基本上是单一源码文件加上其所含入的头文件 (#include files) 。

C++ 对定义于不同编译单元内的 non-local static 对象的初始化次序没有明确定义。

因此，需要将每个 non-local static 对象声明到专属的 reference-returning 函数内。

> 类似单例模式的实现。

```c++
FileSys& getFileSysInstance(){
    static FileSys fs;
    return fs;
}
```

如此可以保证使用时对象的初始化。

但这是线程不安全的。需要在程序的单线程启动阶段 (single-threaded startup portion) 手工调用所有 reference-returning 函数，以消除与初始化有关的「竞速形势 (race conditions)」。

## 2. 构造 / 析构 / 赋值运算

### 2.5 了解 C++ 默认提供的函数与何时调用这些函数

C++ 自动提供如下成员函数（直到被调用时）：

- **默认构造函数** 如果没有定义构造函数
- **默认析构函数** 如果没有定义
- **复制构造函数** 如果没有定义
- **移动构造函数** 如果没有定义
- **移动赋值构造函数** 如果没有定义
- **赋值运算符** 如果没有定义
- **地址运算符** 如果没有定义

除非基类自身声明有 `virtual` 析构函数，否则提供 non-virtual 析构函数。

---

#### 若类内含有 reference 或 const 成员，需手动支持赋值操作

C++ 不允许修改 reference 的指向目标，因为修改目标的数据占用了这个语法。

```c++
Name name1 = Name("Jun");
Name name2 = Name("Zhi");
Name& nameR = name1;

nameR = name2;  // 这将修改 name1 内容，而非使 nameR 指向 name2
```

---

#### 若基类声明赋值运算为 `private` ，则子类不会自动提供赋值运算

自动生成的赋值运算需要依赖基类赋值运算。

### 2.6 若不想使用编译器自动生成函数，应明确拒绝

将明确不需要自动生成的函数声明为 `private` 。

且不要实现之。

前者将在编译期拒绝外部调用，后者将在链接期拒绝内部及友元调用。

构建一个 Uncopyable 类并继承之，可以将拒绝内部及友元调用的警告提前至编译期。

```c++
class Uncopyable{
protected:
    Uncopyable(){};
    ~Uncopyable(){};
private:
    Uncopyable(const Uncopyable&);
    Uncopyable& operator=(const Uncopyable&);
}

class Sth: private Uncopyable{
    ...
}
```

### 2.7 为多态基类声明 virtual 析构函数

如果一个类作为基类，且带有至少一个虚函数，则一定要为之声明 virtual 析构函数。

否则藉由基类指针释放子类对象时，将造成「局部销毁」而内存泄露。

当一个类带有虚函数时，将生成一个 **vptr** (virtual table pointer)。其指向一个由函数指针构成的数组，称为 **vtbl** (virtual table) 。

- 实际被调用的函数取决于该对象的 vptr 所指的 vtbl ，编译器将在其中寻找适当的函数指针。
- 若类含虚函数，其对象的体积将会增加。

因此不要为非多态基类（即不包含虚函数的基类）声明 virtual 析构函数。

> 如 2.6 中的 Uncopyable 类。  
> 这种类不会作为基类指针使用，因此其不具备多态性。

由于 C++ 缺乏禁止派生的机制，因此一般约定：

- 不允许继承不带 virtual 析构函数的类。

#### 抽象类与析构函数

作为抽象类，应有：

- 带有 **纯虚析构函数** 。
- 为纯虚析构函数提供的一个定义。

纯虚函数是可以拥有定义的，且纯虚析构函数将被子类析构函数调用，如无定义则将发生链接错误。

### 2.8 不要向析构函数外抛出异常

一般有两种方案解决析构函数抛出异常：

- 记录并调用 `abort()` 终止。
- 仅记录。

```c++
DBConn::~DBConn(){
    try{ db.close(); }
    catch(...){
        // 记录
        std::abort();
    }
}
```

```c++
DBConn::~DBConn(){
    try{ db.close(); }
    catch(...){
        // 记录
    }
}
```

但是这两种方式都无法对「导致 close 抛出异常」的情况做出反应。

因此有如下解决方案，以向客户提供处理错误的机会：

```c++
class DBConn{
public:
    void close(){
        if( !closed ){
            db.close();
            closed = true;
        }
    }
    ~DBConn(){
        if( !closed ){
            try{ db.close();}
            catch{
                // 记录
                ...
                // 吞下异常或继续
            }
        }
    }
}
private:
    DBConnection db;
    bool closed;
```

### 2.9 绝不在构造和析构过程中调用 virtual 函数

调用子类的构造函数与析构函数时，会调用父类的构造函数与析构函数。具体有如下顺序：

- 先调用父类构造函数，再调用子类构造函数。
- 先调用子类析构函数，再调用父类析构函数。

明显，在父类的构造函数与析构函数中，首先子类是不可见的，其次子类的成员也并为初始化，因此调用子类的虚函数是不可行且不安全的。

因此，不应在构造和析构 **过程** 中调用虚函数。这个过程包括构造和析构函数本身及其调用的其他函数。

### 2.10 令 `operator=` 返回一个 reference to `*this`

对于内置类型，可以使用连锁赋值：

```c++
int x,y,z;
x = y = z = 15;
```

由于赋值运算符具有 R-L 结合性，因此从右开始运行，即：

```c++
int x,y,z;
x = (y = (z = 15));
```

对于重载了赋值运算符的自定义类型，也应使其保持这种特性。因此赋值运算符必须返回一个 reference 指向运算符的左侧实参。

此条同样适用于 `+=` `-=` 等其他赋值相关运算符。

### 2.11 在赋值运算符中处理「自我赋值」

自我赋值可能是潜在的、难以避免的。

```c++
a[i] = a[j]
```

但自我赋值可能会造成严重的问题，如内存泄露，其常发于指针对象。

```c++
Widget& Widget::operator=(const Widget& rhs){
    delete this->pb;
    this->pb = new WidgetContent(*rhs.pb);   // 若为自我赋值则出现问题。
    return *this;
}
```

传统做法采用 **证同测试** (identity test) 检验是否自我赋值。

方法一（证同测试）：

```c++
Widget& Widget::operator=(const Widget& rhs){
    if( this == &rhs) return *this;

    delete this->pb;
    this->pb = new WidgetContent(*rhs.pb);
    return *this;
}
```

传统方法虽然解决了「自我赋值安全性」，但仍不具备「异常安全性」。

- 可能处于其他原因，指针指向的内容仍为空（内存未成功分配等）。

如果解决「异常安全性」，则「自我赋值安全性」也将被避免。

方法二：

```c++
Widget& Widget::operator=(const Widget& rhs){
    WidgetContent* pOrig = this->pb;
    this->pb = new WidgetContent(*rhs.pb);
    delete pOrig;
    return *this;
}
```

方法三（copy and swap 技术）：

```c++
class Widget{
    void swap(Widget& rhs);
}

Widget& Widget::operator=(const Widget& rhs){
    Widget temp(rhs);
    swap(temp);
    return *this;
}
```

可缩写为：

```c++
class Widget{
    void swap(Widget& rhs);
}

Widget& Widget::operator=(Widget rhs){
    swap(rhs);
    return *this;
}
```

不清晰但编译器可能会生成更高效的代码。

### 2.12 赋值对象时不要忘记每个成分

拷贝构造函数与赋值函数统称 copying 函数。

有如下代码：

```c++
class Drived: public Base{
public:
    Drived(const Drived& rhs);
    Drived& operator=(const Drived& rhs);
private:
    int num;
}

Drived::Drived(const Drived& rhs):num(rhs.num){};
Drived& Drived::operator=(const Drived& rhs){
    this->num = rhs.num;
}
```

如上 copying 函数只顾及了子类中的内容，而忽视了对基类内容的拷贝。

应改为：

```c++
class Drived: public Base{
public:
    Drived(const Drived& rhs);
    Drived& operator=(const Drived& rhs);
private:
    int num;
}

Drived::Drived(const Drived& rhs):Base(rhs),num(rhs.num){};
Drived& Drived::operator=(const Drived& rhs){
    Base::operator=(rhs);
    this->num = rhs.num;
}
```

注意两个不同的 copying 函数在实现上的语法区别。

通过两者之间相互调用以避免代码重复是不合理的。

可以通过抽取公共操作，定义新的成员函数 (init) 消除代码重复。

### 3.13 以对象管理资源

对象在作用域结束时将被自动析构。将资源放入对象，可实现自动管理。

使用类指针对象（智能指针）封装指针，可以实现对象指针的自动管理。

```c++
void f(){
    std::shared_ptr<Student> pStu(createStuInstance());
}
```

- 获得资源后立即放入管理对象
- 管理对象运用析构函数确保资源被释放。

#### 不要将动态内存分配得到的数组作为智能指针的初始化形参

智能指针底层使用 `delete` 而非 `delete[]` 释放资源。因此以下行为将引发不可见的预期外错误。

```c++
std::shared_ptr<std::string> strs(new std::string[10]);
```