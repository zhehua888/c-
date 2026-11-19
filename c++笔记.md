# 									c++笔记

## 1.一二级指针的转换：

```c++
		//总结const和指针的类型转换公式：
		//一级指针
		int*  <=  const int*	是错误的!
		const int*	<= int*		是可以的!
        //二级指针
		int** <= const int**	是错误的!
		const int** <= int**	是错误的!
		int** <= int *const*	是错误的!
		int*const* <= int**		是可以的!
```

## 2.C++的引用

### 		1）引用和指针的区别？

1. 引用是一种更安全的指针
1. 引用是必须初始化的，指针可以不初始化
2. 引用只有一级引用，没有多级引用；指针可以有一级指针，也可以有多级指针
3. 定义一个引用变量，和定义一个指针变量，其汇编指令是一模一样的；通过引用变量修改所引用内存的值，和通过指针解引用修改指针指向的内存的值，其底层指令也是一模一样的

### 		2）左值引用和右值引用：

​		**右值引用**

​		1.int &&c =20；专门用来引用右值类型，指令上，可以自动产生临时量，然后直接引用临时量 C = 40;
​				2.值引用变量本身是一个左值，只能用左值引用来引用它
​				3.不能用一个右值引用变量，来引用一个左值

​		**左值引用**

​		int a =10；//左值，它有内存，有名字，值可以修改的

## 3.const, 一级指针， 引用的结合引用

```c++
		//int a = 10;
		//int *p = &a;
		//const int *&q = p;
		//const int **q = &p; //		const int** <= int**
```

## 4.c++ new\malloc，delete\free 区别

​		**malloc和free, 称作C的库函数**
​				**new和delete, 称作运算符**

### 		1）new和mal1oc的区别是什么？delete和free的区别是什么？

```c++
		//malloc
		int *p = (int*)malloc(sizeof(int));
        if (p == nullptr)
            return -1;
        *p = 20;
        free(p);
	
		//new
		int *p1 = new int(20);
		delete p1;


		int *q = (int*)malloc(sizeof(int) * 20);
        if (q == nullptr)
        return -1;
        free(q);

        //int *q1 = new int[20];
        int *q1 = new int[20]();
        delete[]q1;
```

​	1.new不仅可以做内存开辟，还可以做内存初始化操作
​			2.malloc开辟内存失败，是通过返回值和nullptr做比较；而new开辟内存失败，是通过抛出bad_alloc类型的异常来判断的。

### 		2）new有多少种？

```c++
  	    int *p1 = new int(20);
        int *p2 = mew (nothrow) int;
        const int *p3 = new const int(40);


        //定位new
        int data = 0;
        int *p4 = new (&data) int(50);
        cout << "data:" << data << endl;
```

## 5.C++的类

c++：类   		=》	实体的抽象类型

实体（属性、行为）  ->   ADT(abstract data type) 
              |
			对象                                 <- (实例化) 类（属性->成员变量   行为->成员方法）

**OOP语言的四大特征是什么？**
				抽象      封装/隐藏      继承      多态

​		类  ->  商品实体
​				访问限定符：public公有的	 private私有的 	protected保护的

**类的成员方法一经编译，所有的方法参数，都会加一个this指针，接收
		调用该方法的对象的地址**

## 6.浅拷贝与深拷贝：

深浅拷贝是面试经典问题，也是常见的一个坑

浅拷贝：简单的赋值拷贝操作

深拷贝：在堆区重新申请空间，进行拷贝操作



**对象默认的拷贝构造是做内存的数据拷贝**
		**关键是对象如果占用外部资源，那么浅拷贝就出现问题了！**

## 7.类的初始化列表：

成员变量的初始化和它们定义的顺序有关,和构造函数初始化列表中出现的先后顺序无关!

## 8.类的各种成员：

**static成员变量一定要在类外进行定义并且初始化**

普通的成员方法 	=>	 编译器会添加一个this形参变量
1. 属于类的作用域
2. 调用该方法时,需要依赖一个对象(常对象是无法调用的         实参:const CGoods*
3. 可以任意访问对象的私有成员                  protected继承 public private 

static静态成员方法=>不会生成this形参

1. 属于类的作用域
2. 用类名作用域来调用方法
3. 可以任意访问对象的私有成员,仅限于不依赖对象的成员(只能调用其它的static静态成员)

const常成员方法  =>  const CGoodsI*this

1. 属于类的作用域
2. 调用依赖一个对象,普通对象或者常对象都可以
3. 可以任意访问对象的私有成员,但是只能读,而不能写



## 9.函数模板：

函数模板：写代码的“模具”，用 `template` 定义，类型暂用 `T` 代替
		模板的实例化：编译器根据模板**生成具体函数代码**的过程
		模板函数：实例化后**真正生成的函数**

模板类型参数：typename/calss
		模板非类型参数:传常量值（如 `int N`），必须编译期确定

模板的实参推演 => 可以根据用户传入的实参的类型，来推导出模板类型参数类型

模板的特例化(专用化):
		模板函数、模板的特例化、非模板函数的重载关系



## 10.vector类模板

### 	1).空间配置器实现的类模板vector

```c++
#include <iostream>

template <typename T>
struct Allocator
{
    //内存开辟
    T* allocate(size_t size)
    {
        return (T*)malloc(sizeof(T) * size);
    }
    //内存的释放
    void deallocate(void *p)
    {
        free(p);
    }
    //负责对象的构造
    void construct(T *p , const T &val)
    {
        new (p) T(val);
    }
    //负责对象的析构
    void destroy(T *p)
    {
        p->~T();
    }
};

template <typename T , typename Alloc = Allocator<T>>
class Vector
{
public:
    Vector(const int size = 20)
    //    : _first(new T[size])
    //    , _last(_first)
     //   , _end(_first + size)
        //, _allocator(alloc)
    {
        // 需要把内存开辟和对象构造分开处理
        _first = _allocator.allocate(size);
        _last = _first;
        _end = _first + size;
    }

    Vector(const Vector<T> &p)
    {
        int size = p._end - p._first;
        //_first = new T[size];

        _first = _allocator.allocate(size);
        int len = p._last - p._first;
        for(int i=0;i<len;i++)
        {
            //_first[i] = p._first[i];
            _allocator.constryct(_first + i , p._first[i]);
        }
        _last = _first + len;
        _end = _first + size;
    }
    Vector<T>& operator=(const Vector<T> &p)
    {
        if(this == &p) return *this;
        //delete []_first;
        for(T *p = _first;p!=_last;p++)
        {
            _allocator.destroy(p);  //把_first指针指向的有效元素进行析构
        }
        _allocator.deallocate(_first);

         int size = p._end - p._first;
        //_first = new T[size];

        _first = _allocator.allocate(size);
        int len = p._last - p._first;
        for(int i=0;i<len;i++)
        {
            //_first[i] = p._first[i];
            _allocator.construct(_first + i , p._first[i]);
        }
        _last = _first + len;
        _end = _first + size;
        return *this;
    }

    void push_back(const T &val)
    {
        if(full()) return;
        //*_last++ = val;
        _allocator.construct(_last , val);
        _last++;
    }

    void pop_back()
    {
        if(empty()) return;
        //_last--;
        _allocator.destroy(--_last);
    }
    const T& front() const
    {
        return *_first;
    }
    const T& back() const
    {
        return *(_last-1);
    }
    bool full()const { return _last == _end; }
    bool empty()const { return _first == _last; }
    int size() const { return _last - _first; } 
    ~Vector()
    {
        //delete []_first;
        for(T *p = _first;p!=_last;p++)
        {
            _allocator.destroy(p);  //把_first指针指向的有效元素进行析构
        }
        _allocator.deallocate(_first);
        _first = _last = _end = nullptr;
    }
private:
    T *_first;
    T *_last;
    T *_end;
    Alloc _allocator;   //定义容器的空间配置器对象

    void expend()
    {
        int size = _end - _first;
        //T *ptmp = new T[2 * size];
        T *ptmp = _allocator.allocate(size * 2);
        for (int i = 0; i < size; i++)
        {
            _allocator.construct(ptmp+i , _first[i]);


            //ptmp[i] = _first[i];
        }
        //delete[]_first;
        for(T *p = _first ; p != _last ;p++)
        {
            _allocator.destroy(p);
        }
        _allocator.deallocate(_first);
        _first = ptmp;
        _last = _first + size;
        _end = _first + 2 * size;
    }
};

class Test
{
public:
    Test() { std::cout << "Test()" << std::endl; }
    ~Test() { std::cout << "~Test()" << std::endl; }
    Test(const Test&) { std::cout<<"Test(const Test&)"<<"\n"; }
};


int main()
{
    Test t1,t2,t3;
    std::cout<<"---------"<<"\n";
    Vector<Test> v;
    v.push_back(t1);
    v.push_back(t2);
    v.push_back(t3);
    std::cout<<"---------"<<"\n";
    v.pop_back();
    std::cout<<"---------"<<"\n";
    return 0;
}
```

### 	2).空间配置器和迭代器实现的类模板vector

```c++
#include <iostream>

template <typename T>
struct Allocator
{
    //内存开辟
    T* allocate(size_t size)
    {
        return (T*)malloc(sizeof(T) * size);
    }
    //内存的释放
    void deallocate(void *p)
    {
        free(p);
    }
    //负责对象的构造
    void construct(T *p , const T &val)
    {
        new (p) T(val);
    }
    //负责对象的析构
    void destroy(T *p)
    {
        p->~T();
    }
};

template <typename T , typename Alloc = Allocator<T>>
class Vector
{
public:
    Vector(const int size = 20)
    //    : _first(new T[size])
    //    , _last(_first)
     //   , _end(_first + size)
        //, _allocator(alloc)
    {
        // 需要把内存开辟和对象构造分开处理
        _first = _allocator.allocate(size);
        _last = _first;
        _end = _first + size;
    }

    Vector(const Vector<T> &p)
    {
        int size = p._end - p._first;
        //_first = new T[size];

        _first = _allocator.allocate(size);
        int len = p._last - p._first;
        for(int i=0;i<len;i++)
        {
            //_first[i] = p._first[i];
            _allocator.constryct(_first + i , p._first[i]);
        }
        _last = _first + len;
        _end = _first + size;
    }
    Vector<T>& operator=(const Vector<T> &p)
    {
        if(this == &p) return *this;
        //delete []_first;
        for(T *p = _first;p!=_last;p++)
        {
            _allocator.destroy(p);  //把_first指针指向的有效元素进行析构
        }
        _allocator.deallocate(_first);

         int size = p._end - p._first;
        //_first = new T[size];

        _first = _allocator.allocate(size);
        int len = p._last - p._first;
        for(int i=0;i<len;i++)
        {
            //_first[i] = p._first[i];
            _allocator.construct(_first + i , p._first[i]);
        }
        _last = _first + len;
        _end = _first + size;
        return *this;
    }

    void push_back(const T &val)
    {
        if(full()) return;
        //*_last++ = val;
        _allocator.construct(_last , val);
        _last++;
    }

    void pop_back()
    {
        if(empty()) return;
        //_last--;
        _allocator.destroy(--_last);
    }
    const T& front() const
    {
        return *_first;
    }
    const T& back() const
    {
        return *(_last-1);
    }
    bool full()const { return _last == _end; }
    bool empty()const { return _first == _last; }
    int size() const { return _last - _first; } 

    T& operator[](int index)
    {
        if(index < 0 || index >= size())
        {
            throw "OutOfRangeException";
        }
        return _first[index];
    }

    //迭代器一般实现为容器的嵌套类型
    class iterator
    {
    public:
        iterator(T* p = nullptr) : _p(p)
        {}
        bool operator!=(const iterator &it) const
        {
            return _p != it._p;
        }
        void operator++()
        {
            _p++;
        }
        T& operator*()
        {
            return *_p;
        }
        const T& operator*() const
        {
            return *_p;
        }
    private:
        T *_p;
    };

    //提供begin()和end();
    iterator begin() { return iterator(_first); }
    iterator end() { return iterator(_last); }





    ~Vector()
    {
        //delete []_first;
        for(T *p = _first;p!=_last;p++)
        {
            _allocator.destroy(p);  //把_first指针指向的有效元素进行析构
        }
        _allocator.deallocate(_first);
        _first = _last = _end = nullptr;
    }
private:
    T *_first;
    T *_last;
    T *_end;
    Alloc _allocator;   //定义容器的空间配置器对象

    void expend()
    {
        int size = _end - _first;
        //T *ptmp = new T[2 * size];
        T *ptmp = _allocator.allocate(size * 2);
        for (int i = 0; i < size; i++)
        {
            _allocator.construct(ptmp+i , _first[i]);


            //ptmp[i] = _first[i];
        }
        //delete[]_first;
        for(T *p = _first ; p != _last ;p++)
        {
            _allocator.destroy(p);
        }
        _allocator.deallocate(_first);
        _first = ptmp;
        _last = _first + size;
        _end = _first + 2 * size;
    }
};

class Test
{
public:
    Test() { std::cout << "Test()" << std::endl; }
    ~Test() { std::cout << "~Test()" << std::endl; }
    Test(const Test&) { std::cout<<"Test(const Test&)"<<"\n"; }
};


int main()
{
    Vector<int> v1;
    srand(time(NULL));
    for(int i=0;i<=10;i++)
    {
        v1.push_back(rand() % 100);
    }
    //1.
    int size = v1.size();
    for(int i=0;i<size;i++)
    {
        std::cout<<v1[i]<<" ";
    }
    std::cout<<"\n";

    //2.
    auto it = v1.begin();
    for( ;it != v1.end();++it)
    {
        std::cout<<*it<<" ";
    }
    std::cout<<"\n";

    //3.
    for(auto i : v1)
    {
        std::cout<<i<<" ";
    }
    std::cout<<"\n";
}
```



## 11.c++的运算符重载：

```c++
#include <iostream>

class CComplex
{
public:
    CComplex(int r = 0,int i = 0)
        :mreal(r) , mimage(i)
        {}

    // CComplex operator+(const CComplex &p)
    // {
    //     // CComplex tmp;
    //     // tmp.mreal = this->mreal + p.mreal;
    //     // tmp.mimage = this->mimage + p.mimage;
    //     // return tmp;
    //     return CComplex(this->mreal+p.mreal,this->mimage+p.mimage);
    // }
    void show()
    {
        std::cout<<mreal<<"+"<<mimage<<"i"<<"\n";
    }
    CComplex operator++(int)
    {
        // CComplex tmp = *this;
        // mreal++;
        // mimage++;
        // return tmp;
        return CComplex(this->mreal++,this->mimage++);
    }
    CComplex& operator++()
    {
        mreal++;
        mimage++;
        return *this;
    }
    void operator+=(const CComplex &p)
    {
        mreal += p.mreal;
        mimage += p.mimage;
    }
private:
    int mreal;
    int mimage;
    friend CComplex operator+(const CComplex &p , const CComplex &q);
    friend std::ostream& operator<<(std::ostream &out,const CComplex &p);
    friend std::istream& operator>>(std::istream &in, CComplex &src);
};


CComplex operator+(const CComplex &p , const CComplex &q)
{
    return CComplex(p.mreal+q.mreal,p.mimage+q.mimage);
}

std::ostream& operator<<(std::ostream &out,const CComplex &p)
{
    out<<p.mreal<<"+"<<p.mimage<<"i"<<"\n";
    return out;
}
std::istream& operator>>(std::istream &in, CComplex &src)
{
    in >> src.mreal >> src.mimage;
    return in;
}
int main()
{
    CComplex comp1(10,10);
    CComplex comp2(20,20);

    CComplex comp3 = comp1 + comp2;
    comp3.show();
    CComplex comp4 = comp1 + 20;
    comp4.show();
    // 编译器做对象运算的时候，会调用对象的运算符重载函数（优先调用成员方法）；如果没有成员方法
    // ，就在全局作用域找合适的运算符重载函数
    CComplex comp5 = 30 + comp1;
    comp5.show();
    comp5 = comp1++;
    comp1.show();
    comp5.show();
    comp5 = ++comp1;
    comp1.show();
    comp5.show();

    comp1 += comp2;
    comp1.show();

    std::cout<<comp1<<"\n";
    return 0;
}
```

##  12.string类：

### 	1).类的实现：

```c++
#include <iostream>
#include <cstring>

//实现一个string类
class String
{
public:
    String(const char *p = nullptr)
    {
        if(p != nullptr) 
        {
            _pstr = new char[strlen(p) + 1];
            strcpy(_pstr , p);
        }
        else 
        {
            _pstr = new char[1];
            *_pstr = '\0';
        }
    }
    String(const String &p)
    {
        _pstr = new char[strlen(p._pstr) + 1];
        strcpy(_pstr , p._pstr);
    }
    String(char *p,bool)
    {
        _pstr = p;
    }
    String &operator=(const String &p)
    {
        if(this == &p) return *this;
        delete []_pstr;
        _pstr = new char[strlen(p._pstr) + 1];
        strcpy(_pstr , p._pstr);

        return *this;
    }
    bool operator>(const String &p) const
    {
        return strcmp(this->_pstr,p._pstr) > 0;
    }
    bool operator<(const String &p) const
    {
        return strcmp(this->_pstr,p._pstr) < 0;
    }
    bool operator==(const String &p) const
    {
        return strcmp(this->_pstr,p._pstr) == 0;
    }
    int length() const
    {
        return strlen(_pstr);
    }
    char& operator[](int i)
    {
        return _pstr[i];
    }
    const char& operator[](int i) const
    {
        return _pstr[i];
    }
    const char* c_str() const
    {
        return _pstr;
    }
    ~String()
    {
        delete []_pstr;
        _pstr = nullptr;
    }
private:
    char *_pstr;
    friend std::ostream &operator<<(std::ostream &out , const String &p);
    friend String operator+(const String &p , const String &q);
};


String operator+(const String &p , const String &q)
{
    char *ptmp = new char[strlen(p._pstr) + strlen(q._pstr) + 1];
    strcpy(ptmp , p._pstr);
    strcat(ptmp , q._pstr);
    //效率低下 ，2次new
    // String tmp(ptmp);
    // delete []ptmp;
    // return tmp;
    return String(ptmp,true);
}

std::ostream &operator<<(std::ostream &out , const String &p)
{
    out<<p._pstr;
    return out;
}



int main()
{
    String str1;
    String str2 = "aaa"; // string(const char*)
    String str3 = "bbb";
    String str4 = str2 + str3;
    String str5 = str2 + "ccc";
    String str6 = "ddd" + str2;

    std::cout << "str6:" << str6 << std::endl;
    // bool operator>(const String &str)
    if (str5 > str6)
    {
        std::cout << str5 << " > " << str6 << std::endl;
    }
    else
    {
        std::cout << str5 << " < " << str6 << std::endl;
    }

    int len = str6.length();
    for (int i = 0; i < len; ++i)
    {
        //    char& str6.operator[](i)
        std::cout << str6[i] << " ";
    }
    std::cout << std::endl;

    // string -> char*
    char buf[1024] = { 0 };
    strcpy(buf, str6.c_str());
    std::cout << "buf:" << buf << std::endl;
    return 0;
}


```



### 	2).string字符串对象的迭代器iterator实现

迭代器的功能： **提供一种统一的方式，来透明地遍历容器**

```c++
#include <iostream>
#include <cstring>
//实现一个string类
class String
{
public:
    String(const char *p = nullptr)
    {
        if(p != nullptr) 
        {
            _pstr = new char[strlen(p) + 1];
            strcpy(_pstr , p);
        }
        else 
        {
            _pstr = new char[1];
            *_pstr = '\0';
        }
    }
    String(const String &p)
    {
        _pstr = new char[strlen(p._pstr) + 1];
        strcpy(_pstr , p._pstr);
    }
    String(char *p,bool)
    {
        _pstr = p;
    }
    String &operator=(const String &p)
    {
        if(this == &p) return *this;
        delete []_pstr;
        _pstr = new char[strlen(p._pstr) + 1];
        strcpy(_pstr , p._pstr);

        return *this;
    }
    bool operator>(const String &p) const
    {
        return strcmp(this->_pstr,p._pstr) > 0;
    }
    bool operator<(const String &p) const
    {
        return strcmp(this->_pstr,p._pstr) < 0;
    }
    bool operator==(const String &p) const
    {
        return strcmp(this->_pstr,p._pstr) == 0;
    }
    int length() const
    {
        return strlen(_pstr);
    }
    char& operator[](int i)
    {
        return _pstr[i];
    }
    const char& operator[](int i) const
    {
        return _pstr[i];
    }
    const char* c_str() const
    {
        return _pstr;
    }
    ~String()
    {
        delete []_pstr;
        _pstr = nullptr;
    }
    // 给String字符串类型提供迭代器的实现
    class iterator
    {
    public:
        iterator(char *p = nullptr) : _p(p)
        {}

        bool operator!=(const iterator &p)
        {
            return this->_p != p._p;
        }
        void operator++()
        {
            ++_p;
        }
        char& operator*()
        {
            return *_p;
        }
    private:
        char * _p;
    };

    // begin返回的是容器底层首元素的迭代器的表示
    iterator begin()
    {
        return iterator(_pstr);
    }
    // end返回的是容器末尾元素后继位置的迭代器的表示
    iterator end()
    {
        return iterator(_pstr + length());
    }




private:
    char *_pstr;
    friend std::ostream &operator<<(std::ostream &out , const String &p);
    friend String operator+(const String &p , const String &q);
};


String operator+(const String &p , const String &q)
{
    char *ptmp = new char[strlen(p._pstr) + strlen(q._pstr) + 1];
    strcpy(ptmp , p._pstr);
    strcat(ptmp , q._pstr);
    //效率低下 ，2次new
    // String tmp(ptmp);
    // delete []ptmp;
    // return tmp;
    return String(ptmp,true);
}

std::ostream &operator<<(std::ostream &out , const String &p)
{
    out<<p._pstr;
    return out;
}



int main()
{
    String s1 = "Hello World";
    //String::iterator it = s1.begin();
    auto it = s1.begin();
    for( ;it != s1.end();++it)
    {
        std::cout<<*it;
    }
    std::cout<<"\n";
    //底层还是迭代器便利
    for(auto i : s1)
    {
        std::cout<<i;
    }
    std::cout<<"\n";
    return 0;
}


```

## 13.什么是容器迭代起的失效问题：

### 1).迭代器为什么会失效？

a：当容器调用erase方法后，当前位置到容器末尾元素的所有的迭代器全部失效了
		b：当容器调用insert方法后，当前位置到容器末尾元素的所有的迭代器全部失效了
					迭代器依然有效 		迭代器全部失效
					首元素 -> 插入点/删除点 -> 末尾元素

c:insert来说，如果引起容器内存扩容
						原来容器的所有的迭代器就全部失效了
					首元素 -> 插入点/删除点 -> 末尾元素

### 2).迭代器失效后，怎么解决？

​	更新迭代器

```c++
#include <iostream>
#include <vector>

/*
迭代器的失效问题？
*/
int main()
{
    std::vector<int> vec;
    for (int i = 0; i < 20; ++i)
    {
        vec.push_back(rand() % 100 + 1);
    }

    for(int i : vec)
    {
        std::cout<<i<<" ";
    }
    std::cout<<"\n";
#if 0
    auto it = vec.begin();
    for( ;it != vec.end();)
    {
        // 迭代器失效的问题，第一次调用erase以后，迭代器it就失效了
        if(*it % 2 == 0)
        {
            it = vec.erase(it);
        }
        else ++it;

    }
#endif
    auto it = vec.begin();
    for (; it != vec.end(); ++it)
    {
        if (*it % 2 == 0)
        {
            vec.insert(it, *it-1);
            ++it;
        }
    }

    for(int i : vec)
    {
        std::cout<<i<<" ";
    }
    std::cout<<"\n";

#if 0 
// 给vec容器中所有的偶数前面添加一个小于偶数值1的数字
    auto it = vec.begin();
    for (; it != vec.end(); ++it)
    {
        if (*it % 2 == 0)
        {
            vec.insert(it, *it-1);
            //break;
        }
    }
#endif

//删除偶数
//迭代器不能边遍历，边删除
#if 0
    auto it = vec.begin();
    for( ;it != vec.end();++it)
    {
        // 迭代器失效的问题，第一次调用erase以后，迭代器it就失效了
        if(*it != 0)
        {
            vec.erase(it);
        }
       // break;
    }
#endif
    return 0;
}
```

### 3).vectorr容器解决迭代器失效：

```c++
#include <iostream>

template <typename T>
struct Allocator
{
    //内存开辟
    T* allocate(size_t size)
    {
        return (T*)malloc(sizeof(T) * size);
    }
    //内存的释放
    void deallocate(void *p)
    {
        free(p);
    }
    //负责对象的构造
    void construct(T *p , const T &val)
    {
        new (p) T(val);
    }
    //负责对象的析构
    void destroy(T *p)
    {
        p->~T();
    }
};

template <typename T , typename Alloc = Allocator<T>>
class Vector
{
public:
    Vector(const int size = 20)
    //    : _first(new T[size])
    //    , _last(_first)
     //   , _end(_first + size)
        //, _allocator(alloc)
    {
        // 需要把内存开辟和对象构造分开处理
        _first = _allocator.allocate(size);
        _last = _first;
        _end = _first + size;
    }

    Vector(const Vector<T> &p)
    {
        int size = p._end - p._first;
        //_first = new T[size];

        _first = _allocator.allocate(size);
        int len = p._last - p._first;
        for(int i=0;i<len;i++)
        {
            //_first[i] = p._first[i];
            _allocator.construct(_first + i , p._first[i]);
        }
        _last = _first + len;
        _end = _first + size;
    }
    Vector<T>& operator=(const Vector<T> &p)
    {
        if(this == &p) return *this;
        //delete []_first;
        for(T *p = _first;p!=_last;p++)
        {
            _allocator.destroy(p);  //把_first指针指向的有效元素进行析构
        }
        _allocator.deallocate(_first);

         int size = p._end - p._first;
        //_first = new T[size];

        _first = _allocator.allocate(size);
        int len = p._last - p._first;
        for(int i=0;i<len;i++)
        {
            //_first[i] = p._first[i];
            _allocator.construct(_first + i , p._first[i]);
        }
        _last = _first + len;
        _end = _first + size;
        return *this;
    }

    void push_back(const T &val)
    {
        if(full()) expend();
        //*_last++ = val;
        _allocator.construct(_last , val);
        _last++;
    }

    void pop_back()
    {
        if(empty()) return;
        verify(_last-1 , _last);

        // 不仅要把_last指针--，还需要析构删除的元素
        //_last--;
        _allocator.destroy(--_last);
    }
    const T& front() const
    {
        return *_first;
    }
    const T& back() const
    {
        return *(_last-1);
    }
    bool full()const { return _last == _end; }
    bool empty()const { return _first == _last; }
    int size() const { return _last - _first; } 

    T& operator[](int index)
    {
        if(index < 0 || index >= size())
        {
            throw "OutOfRangeException";
        }
        return _first[index];
    }

    //迭代器一般实现为容器的嵌套类型
    class iterator
    {
    public:
        friend class Vector<T, Alloc>;
        iterator(Vector<T,Alloc> *_pvec = nullptr , T* p = nullptr) 
            : _p(p)
            , _pVec(_pvec)
        {
            Iterator_Base *itb = 
                    new Iterator_Base(this, _pVec->_head._next);
            _pVec->_head._next = itb;
        }
        bool operator!=(const iterator &it) const
        {
            if(_pVec == nullptr || _pVec != it._pVec)
            {
                throw "iterator incompatable";
            }
            return _p != it._p;
        }
        void operator++()
        {
            if(_pVec == nullptr)
            {
                throw "iterator invalid";
            }
            _p++;
        }
        T& operator*()
        {
            if(_pVec == nullptr)
            {
                throw "iterator invalid";
            }
            return *_p;
        }
        const T& operator*() const
        {
            if(_pVec == nullptr)
            {
                throw "iterator invalid";
            }
            return *_p;
        }
    private:
        T *_p;
        //给迭代器提供一个当前容器的指针，使迭代器可以知道是哪个容器
        Vector<T,Alloc> * _pVec;
    };

    //提供begin()和end();
    iterator begin() { return iterator(this,_first); }
    iterator end() { return iterator(this,_last); }

//检查迭代器失效
    void verify(T *first , T *last)
    {
        Iterator_Base *pre = &this->_head;
        Iterator_Base *it = this->_head._next;
        while(it != nullptr)
        {
            if(it->_cur->_p >= first && it->_cur->_p <= last)
            {
                //迭代器失效，把iterator持有的容器指针置nullptr
                it->_cur->_pVec = nullptr;
                //删除当前迭代器节点,继续判断后边的迭代器节点是否失效
                pre->_next = it->_next;
                delete it;
                it = pre->_next;
            }
            else
            {
                pre = it;
                it = it->_next;
            }
        }
    }

    iterator insert(iterator it , const T &val)
    {
        //1.不考虑扩容 verify(_first - 1,_last)
        //2.不考虑it._ptr的指针合法性

        verify(it._p-1 , _last);
        T *p = _last;
        while(p > it._p)
        {
            _allocator.construct(p , *(p-1));
            _allocator.destroy(p-1);
            p--;
        }
        _allocator.construct(p , val);
        _last++;
        return iterator(this , p);

    }

    iterator erase(iterator it)
    {
        verify(it._p-1 , _last);
        T *p = it._p;
        while(p < _last - 1)
        {
            _allocator.destroy(p);
            _allocator.construct(p , *(p+1));
            p++;
        }
        _allocator.destroy(p);
        _last--; 
        return iterator(this , it._p);
    }


    ~Vector()
    {
        //delete []_first;
        for(T *p = _first;p!=_last;p++)
        {
            _allocator.destroy(p);  //把_first指针指向的有效元素进行析构
        }
        _allocator.deallocate(_first);
        _first = _last = _end = nullptr;
    }
private:
    T *_first;
    T *_last;
    T *_end;
    Alloc _allocator;   //定义容器的空间配置器对象

    // 容器迭代器失效增加代码
    struct Iterator_Base
    {
        Iterator_Base(iterator *c=nullptr, Iterator_Base *n=nullptr)
            : _cur(c), _next(n) {}
        iterator *_cur;
        Iterator_Base *_next;
    };
    Iterator_Base _head;

    void expend()
    {
        int size = _end - _first;
        //T *ptmp = new T[2 * size];
        T *ptmp = _allocator.allocate(size * 2);
        for (int i = 0; i < size; i++)
        {
            _allocator.construct(ptmp+i , _first[i]);


            //ptmp[i] = _first[i];
        }

        verify(_first, _first + size); // 通知旧区间全部失效

        //delete[]_first;
        for(T *p = _first ; p != _last ;p++)
        {
            _allocator.destroy(p);
        }
        _allocator.deallocate(_first);
        _first = ptmp;
        _last = _first + size;
        _end = _first + 2 * size;
    }
};

class Test
{
public:
    Test() { std::cout << "Test()" << std::endl; }
    ~Test() { std::cout << "~Test()" << std::endl; }
    Test(const Test&) { std::cout<<"Test(const Test&)"<<"\n"; }
};


int main()
{
    Vector<int> v1(200);
    srand(time(NULL));
    for(int i=0;i<=10;i++)
    {
        v1.push_back(rand() % 100);
    }
    for (int v : v1)
    {
        std::cout << v << " ";
    }
    std::cout<<"\n";
#if 0
    auto it = v1.begin();
    for (; it != v1.end(); ++it)
    {
        if (*it % 2 == 0)
        {
            it = v1.insert(it, *it - 1);
            ++it;
        }
    }
#endif


    auto it = v1.begin();
    while (it != v1.end())
    {
        if (*it % 2 == 0)
        {
            // 迭代器失效的问题，第一次调用erase以后，迭代器it就失效了
            //v1.erase(it); // insert(it, val)   erase(it)
            it = v1.erase(it);
        }
        else
        {
            ++it;
        }
    }

    for (int v : v1)
    {
        std::cout << v << " ";
    }


#if 0
    auto it1 = v1.end();  
    //v1.pop_back();     // verify(_last-1, _last)
    auto it2 = v1.end();
    std::cout << (it1 != it2) << std::endl;
    std::cout<<"\n";
#endif
}
```

## 14.new和delete的实现：

​	**new -> operator new**
​			**delete -> operator delete**

### 		面经：

​	**new和delete能混用吗？C++为什么区分单个元素和数组的内存分配和释放呢？**

​		自定义的类类型，有析构函数，为了调用正确的析构函数，那么开辟对象数组的时候，
​				会多开辟4个字节，记录对象的个数

**`	new[]` 会偷偷多要空间**：它会在内存头部额外存一个“数组长度”的 Cookie（比如 8 字节），并连续调用 N 次构造函数。返回的指针是**越过** Cookie 的地址。

**`delete[]` 看本本干活**：它拿到指针后，往前退几个字节找到 Cookie，读出长度 N，然后老老实实调用 N 次析构函数，最后释放整块内存。

**如果 `delete p` 混用**：它不知道是数组，只调用 1 次析构函数，且试图从错误的地址释放内存，直接导致崩溃。



```c++
#include <iostream>

// 先调用operator new开辟内存空间、然后调用对象的构造函数（初始化）
void* operator new(size_t size)
{
    void *p = malloc(size);

    if(p == nullptr)
    {
        throw std::bad_alloc();
    }
    std::cout<<"operator new address"<<p<<"\n";
    return p;
}

// delete p; 调用p指向对象的析构函数、再调用operator delete释放内存空间
void operator delete(void *ptr)
{
    std::cout<<"operator delete addes"<<ptr<<"\n";
    free(ptr);

}

// void* operator new[](size_t size)
// {
//     void *p = malloc(size);

//     if(p == nullptr)
//     {
//         throw std::bad_alloc();
//     }
//     std::cout<<"operator new address"<<p<<"\n";
//     return p;
// }

// void operator delete[](void *ptr)
// {
//     std::cout<<"operator delete addes"<<ptr<<"\n";
//     free(ptr);

// }
int main()
{
    int *p = new int;
    int *q = new int[10];
    delete p;
    delete q;

    return 0;
}
```

## 15.简易对象池：

```c++
#include <iostream>


template <typename T>

class Queue
{
public:
    Queue()
    {
        _front = _rear = new QueueItem();
    }
    ~Queue()
    {
        QueueItem *cur = _front;
        while(cur != nullptr)
        {
            _front = _front->_pnext;
            delete cur;
            cur = _front;
        }
        _front = _rear = nullptr;
    }

    void push(const T &val)
    {
        QueueItem *p = new QueueItem(val);
        _rear->_pnext = p;
        _rear = p;
    }
    void pop()
    {
        if(empty()) return;
        QueueItem *p = _front->_pnext;
        _front->_pnext = p->_pnext;
        if(_front->_pnext == nullptr) _rear = _front;
        delete p;
    }
    T front() const
    {
        return _front->_pnext->_data;
    }
    bool empty() const
    {
        return _front == _rear;
    }
private:
    struct QueueItem
    {
        QueueItem(T data = T()) 
            : _data(data)
            , _pnext(nullptr)
        {}
        void * operator new(size_t size)
        {
            (void)size;
            if(_itemPool == nullptr)
            {
                _itemPool = (QueueItem *)::new char[sizeof(QueueItem) * POOL_ITEM_SIZE];
                QueueItem *p = _itemPool;
                for( ;p < _itemPool + POOL_ITEM_SIZE - 1;++p)
                {
                    p->_pnext = p + 1;
                }
                p->_pnext = nullptr;
            }
            QueueItem *cur = _itemPool;
            _itemPool = _itemPool->_pnext;
            return cur;
        }
        void operator delete(void *ptr)
        {
            QueueItem *p = (QueueItem *)ptr;
            p->_pnext = _itemPool;
            _itemPool = p;
        }
        T _data;
        QueueItem *_pnext;
        static QueueItem *_itemPool;
        static const int POOL_ITEM_SIZE = 10000;
    };

    QueueItem *_front;      //队头
    QueueItem *_rear;       //队尾
};
template <typename T>
typename Queue<T>::QueueItem *Queue<T>
            ::QueueItem::_itemPool = nullptr;


int main()
{
    Queue<int> q;
    for(int i=0;i<10;i++)
    {
        q.push(rand() % 100 + 1);
        q.pop();
    }
    std::cout<<q.empty()<<"\n";
    // while(!q.empty())
    // {
    //     std::cout<<q.front()<<"\n";
    //     q.pop();
    // }
    // std::cout<<"\n";
    return 0;
}
```



## 16.继承的本质和原理

### 		1).继承的本质

​			a.代码的复用

​			b.

### 		2).类和类之间的关系：

​			组合：a part of..一部分的关系
​					继承:a kind of......一种的关系

### 		3).继承关系：

3) ![image-20261116152034502](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20261116152034502.png)

1. **基类的 `private` 成员永远是不可见的**：无论你用哪种方式继承，基类的 `private` 成员在派生类中都无法直接访问（只能通过基类提供的 `public` 或 `protected` 成员函数去访问）。
2. **“就低不就高”原则**：派生类内部看到的权限，永远是 **`基类原本权限`** 和 **`继承方式`** 中**级别更低（限制更严）**的那一个。
3. 外部只能访问对象public的成员，protected和private的成员无法直接访问
4. 在继承结构中，派生类从基类可以继承过来private的成员，但是派生类却无法直接访问
5. protected和private的区别？在基类中定义的成员，想被派生类访问，但是不想被外部访问，
   那么在基类中，把相关成员定义成protected保护的；如果派生类和外部都不打算访问，那么
   在基类中，就把相关成员定义成private私有的。



### 		4）.默认的继承方式是什么?

​				要看派生类是用class定义的，还是struct定义的?
​						class定义派生类，默认继承方式就是private私有的
​						struct定义派生类，默认继承方式就是public私有的



## 17.派生类的构造过程

### 		1).派生类怎么初始化从基类继承来的成员变量呢?

​			**派生类从继承可以继承来所有的成员(变量和方法)，除过构造函数和析构函数**

​			答：通过调用基类相应的构造函数来初始化

### 		2).派生类从基类继承来的成员的初始化和清理由谁负责呢？

​			**派生类的构造函数和析构函数，负责初始化和清理派生类部分**

​			答：是由基类的构造和析构函数来负责

### 		3).派生类对象构造和析构的过程是: 

​			答：

​			1.派生类调用基类的构造函数，初始化从基类承来的成员
​					2.调用派生类自己的构造函数，初始化派生类自己特有的成员
​						.....派生类对象的作用域到期了
​					3.调用派生类的析构函数，释放派生类成员可能占用的外部资源（堆内存，文件)
​					4.调用基类的析构函数，释放派生类内存中，从基类继承来的成员可能占用的外部资源 (堆内存，						文件)



## 18.重载、覆盖（重写）、隐藏

1.重载关系

​	**一组函数要重载，必须处在同一个作用域当中；而且函数名字相同，参数列表不同**

2.隐藏的关系

​	**在继承结构当中，派生类的同名成员，把基类的同名成员给隐藏调用了**

3.覆盖关系：
			**基类和派生类的方法，返回值、函数名以及参数列表都相同，而且基类的方法是虚函数，**
			**那么派生类的方法就自动处理成虚函数，它们之间成为覆盖关系**

 

```c++
#include <iostream>

class Base 
{
public:
    Base(int date = 10) :ma(date)
    {
        std::cout<<"Base()"<<"\n";
    }
    void show() { std::cout << "Base::show()" << std::endl; }
    void show(int) { std::cout << "Base::show(int)" << std::endl; }
    ~Base()
    {
        std::cout<<"~Base()"<<"\n";
    }
protected:
    int ma;
};

class Derive : public Base
{
public:
    Derive(int date = 20) 
        : Base(date) 
        , mb(date)
    {
        std::cout<<"Derive()"<<"\n";
    }
        void show() { std::cout << "Derive::show()" << std::endl; }

    ~Derive()
    {
        std::cout<<"~Derive()"<<"\n";
    }
private:
    int mb;
};
int main()
{
    Base b;
    Derive d;

    // 基类对象b <- 派生类对象d        类型从下到上的转换  Y
    b = d;

    //派生类对象d <- 基类对象b         类型从上到下的转换  N
    // d = b;

    //基类指针（引用）<- 派生类对象     类型从下到上的转换  Y
    Base *pb = &d;

    //派生类指针 (引用) <- 基类对象     类型从上到下的转换  N
    Derive *pd = &b;  

    //总结：在继承结构中进行上下的类型转换，默认只支持从下到上的类型的转换
    return 0;
}
```





## 19.静态绑定和动态绑定

总结一:

静态绑定（早绑定）

编译阶段，根据指针 / 引用**声明类型**确定调用哪个函数，直接写死函数地址，汇编为 call 固定地址；不关		心指针实际指向的对象真实类型。

动态绑定（晚绑定）

编译阶段无法确定真实对象类型，不写死调用地址；运行时通过对象的 fvptr 找到虚表，查表取出对应虚		函数地址放入寄存器，汇编为 call 寄存器；由**对象实际类型**决定调用哪个函数。



一个类里面定义了虚函数，那么编译阶段，编译器给这个类类型产生一个唯一的vftab1e虚函数
		表，虚函数表中主要存储的内容就是RTTI指针（类型字符串）和虚函数的地址。 当程序运行时，每一张		虚函数表都会加载到内存的rodata区

总结二:
		一个类里面定义了虚函数，那么这个类定义的对象，其运行时，内存中开始部分，多存储一个
		vfptr虚函数指针，指向相应类型的虚函数表vftable。一个类型定义的n个对象，它们的vfptr指

向的都是同一张虚函数表

总结三:
		一个类里面虚函数的个数，不影响对象内存大小（一个vfptr)，影响的是虚函数表的大小

总结四:
		如果派生类中的方法，和基类继承来的某个方法，返回值、函数名、参数列表都相同，
		而且基类的方法是virtual虚函数，那么派生类的这个方法，自动处理成虚函数

总结五：

当基类的指针指向派生类时，如果基类里面没有虚函数成员，那么 RTTI 指针指向的就是基类字符，

反之，如果基类里面有虚函数成员，那么 RTTI 指针指向的就是派生类字符

总结六：



![image-20261117110850318](c++笔记.assets/image-20261117110850318-17948849374961.png)

![image-20261117112703799](c++笔记.assets/image-20261117112703799-17948860259052.png)



### 		

### 			1).问题一：哪些函数不能实现成虚函数?

​			虚函数的依赖:
​					1.虚函数能产生地址，存储在vftable当中
​					2.对象必须存在(vfptr -> vftable -> 虚函数地址)

​			构造函数
​					1.virtual+构造函数 No! 		构造函数还没有对象
​					2.构造函数中（调用的任何函数，都是静态绑定的）调用虚函数，也不会发生动态绑定

​			派生类对象构造过程 

​			1.先调用的是基类的构造函数

​			2.才调用派生类的构造函数 

​			static静态成员方法 NO! virtual + static

​			静态成员方法不需要对象调用

### 			2).问题二：什么时候把基类的析构函数必须实现成虚函数?

​			虚析构函数析构函数调用的时候，对象是存在的!

​			基类的指针（引用）指向堆上new出来的派生类对象的时候， delete pb(基类的指针)
​					它调用析构函数的时候，必须发生动态绑定，否则会导致派生类的析构函数无法调用

### 			3).是不是虚函数的调用一定是动态绑定？

​			

​			

20  .多态 vfptr和vftable



21.抽象类的设计原理



22.多重继承以及问题



23.虚基类 vbptr和vbtable



24.RTTI



25.C++四种类型强转



26.继承多态常见笔试面试题分享





















  
