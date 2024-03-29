## C++ Primer读书笔记速查

优美的 STL 类遍历方式
```
vector<std::string> words { "one", "none","some"};

for(auto &i: words)
    i+=i;
```
注意： 仅适用于有 begin/end 的数据类型， int* a, new int[] 不适用

#### 基本内置类型

- 类型char和类型signed char并不一样，char根据编译器 的不同表示为signed char 和 unsigned char，现在大多数计算机表示范围为-128至127
- long一般和int有一样的尺寸，如果超过int，请选用long long
- 在算数表达式中不要使用char和bool
- 执行浮点数运算选用double，因为float精度不够，而双精度浮点数和单精度浮点数的计算代价相当，对于某些机器来说双精度甚至更优
- 无符号类型溢出后的值为对该无符号类型总数取模后的余数，如把-1赋给unsigned char的结果为255
- 有符号数溢出后的结果是未定义的，此时程序可能继续工作、崩溃，也可能产生垃圾数据。
- 表表达式里既有signed又有unsigned时，signed会自动转为unsigned，当signed为负数时，转换后的值是无法预估的
- 字符串字面值类型是由常量字符构成的字符数组，编译器会在每个字符串的结尾添加一个空字符'\0'，所以字符串的实际长度会比内容多1

##### 指定字面值的类型

- 字符合字符串字面值

| 前缀 | 含义          | 类型     |
| ---- | ------------- | -------- |
| u    | Unicode16字符 | char16_t |
| U    | Unicode32字符 | char32_t |
| L    | 宽字符        | wchar_t  |
| u8   | UTF-8         | char     |

- 整形字面值

| 后缀     | 最小匹配类型 |
| -------- | ------------ |
| u or U   | unsigned     |
| l or L   | long         |
| ll or LL | long long    |

- 浮点类型

| 后缀   | 类型        |
| ------ | ----------- |
| f or F | float       |
| l or L | long double |

- 例子

```c++
L'a' //宽字符字面值
u8"hi!" //utf-8字符串字面值；
42ULL   //无符号整形字面值，类型是unsigned long long
1E-3F   //单精度浮点型字面值，类型是float
3.14159L //扩展精度浮点型字面值，类型是long long
```

##### 内置类型变量默认初始化

- 定义于任何函数体之外的变量会被初始化为0
- 定义与行数体内部的内置类型变量将不被初始化
- 一个未被初始化的内置类型变量的值是未定义的
- 建议初始化每一个内置类型的变量

##### 变量声明和定义

- 变量声明：规定了变量的类型和名字
- 变量定义：声明并申请存储空间，可能赋初值
- extern：声明而非定义一个变量，如

```
extern int i;   //声明i，而非定义i
int j;         //声明并定义i
```

- 变量只能被定义一次，但可以被申明多次

##### 标识符

- C++中的标识符由字母、数字、下划线组成，必须以字母或下划线开头
- 标识符中不能连续出现两个下划线，不能以下划线紧连着大写字母开头
- 定义在函数体外的标识符不能以下划线开头

##### 指针

- 指针本身就是一个对象，允许指针赋值和拷贝，允许在指针的生命周期内先后指向不同的对象
- 指针无须在定义时赋初值，和其他内置类型一样，值作用域内定义的指针如果没有初始化将拥有一个不确定的值

##### 空指针

```
//以下三个等价：
int *p1 = nullptr;
int *p2 = 0;
int *p3 = NULL;//这个需要首先#include cstdlib
```

在新的标准下，最好使用nullptr，避免使用NULL

##### void*指针

```
void*是一种特殊类型的指针，可用于存放任意对象的地址,不能直接操作void*指向的对象，因为并不确定该对象是什么类型，不能直接解引用操作。

//注意：
int* p1,p2; //p1是指向int的指针，p2是int
int* p1, *p2; //p1和p2都是指向int的指针
```

##### const限定符

const对象一旦创建后其值就不能再改变，所以const对象必须初始化。

##### constexpr

C++11新标准规定，允许将变量申明为constexpr类型以便由编译器来验证变量的值是否是常量表达式。申明为constexpr的变量一定是一个常量，而且必须使用常量表达式初始化。

一般来说，如果认定某变量是一个常量表达式，那就把它申明为constexpr类型。

算数类型、引用类型和指针都属于字面值类型。

尽管指针和引用都能定义成constexpr，但它们的初始值却受到严格限制，一个constexpr指针初始值必须是nullptr或者0，或者是存在于某个固定地址中的对象。

函数体内定义的变量一般来说并非存放在固定地址中，因此constexpr指针不能指向这样的变量。相反，定义于所有函数体外的对象，其地址固定不变，能用来初始化constexpr指针，除非在函数体内定义具有固定地址的的变量。

##### auto

auto让编译器通过初始值来推算变量的类型，auto定义的变量必须有初始值。

##### decltype

```
decltype(f(x)) sum = x; //sun的类型就是函数f返回的类型
const int ci = 0, &cj = ci;
decltype(ci) x = 0; //x的类型是const int
decltype(cj) y = x; //y的类型是const int&, y变量绑定到x
decltype(ci) z; //错误：z是一个引用，必须初始化
```

decltype的作用是选择并返回操作数的数据类型

##### 头文件保护符

- #define指令把一个名字设定为预处理变量
- #ifdef当且仅当变量已定义为真
- #ifndef当且仅当变量未定义为真
- 一旦检查结果为真，则执行后续操作指导遇见#endif

为了避免与程序中其他实体发生名字冲突，一般把预处理变量的名字全部大写。

##### size_t

size_t和int区别:
size_t是一些C/C++标准在stddef.h中定义的。这个类型足以用来表示对象的大小。size_t的真实类型与操作系统有关。


在32位架构中被普遍定义为：

typedef   unsigned int size_t;

而在64位架构中被定义为：

typedef  unsigned long size_t;
        size_t在32位架构上是4字节，在64位架构上是8字节，在不同架构上进行编译时需要注意这个问题。而int在不同架构下都是4字节，与size_t不同；且int为带符号数，size_t为无符号数。

为什么有时候不用int，而是用size_type或者size_t:

与int固定四个字节不同有所不同,size_t的取值range是目标平台下最大可能的数组尺寸,一些平台下size_t的范围小于int的正数范围,又或者大于unsigned int. 使用Int既有可能浪费，又有可能范围不够大。



##### 数组

```
#include <iostream>
int main ()
{
    int arr[5];//stack of the computer

    //C like array allocation
    int *arr_m = (int *) malloc(sizeof (int) * 5);

    //C++ like array allocation, stays in the heap
    int *arr_new = new int[5]; //脏数据数组
    int *str = new string[5];    //空字符串数组
    int *b = new int[5]{0};    //   [0,0,0,0,0]
    int *c = new int[5]();    //[0,0,0,0,0]  注意是圆括号

    int arr2[5][5];
    //C  
    int **arr_2d_m = (int **) malloc(sizeof (int*) * 5);
    for(int i = 0; i < 5; i ++)
        arr_2d_m[i] = (int *) malloc(sizeof (int) * 5);

    //C++  
    int **arr_2d_new = new int*[5];
    for(int i = 0; i < 5; i ++)
        arr_2d_new[i] = new int[5];

     return 0;
}

```


数组不允许拷贝和赋值：

```
int a[] = {1,2,3}; 
int b[] = a;	错误：不允许使用一个数组初始化另一个数组
b = a;		错误：不能把一个数组直接赋值给另一个数组
```

只要指针指向的是数组中的元素，或者数组中尾元素的下一位置，都可以执行下标运算。

```
int a[] = {0,2,4,6,8};
int *p = &a[2];	//p指向索引为2的元素
int j = p[1];	//p[1]等价于*(p+1)，为a[3]
int k = p[-2];	//等价于a[0]
```

虽然标准库类型string和vector也能执行下标运算，但是标准库类型限定使用的下标必须是无符号类型，而内置的下标运算无此要求。

##### 多维数组

```
int a[10][2][3] = {0}	//将所有元素初始化为0
//内层嵌套的花括号并非必需
int a[3][4] = {0,1,2,3,4,5,6,7,8,9,10,11};
```

对多维数组操作需用引用：

```
int a[2][2] = {1,2,3,4};
int cnt = 5;
for(auto &row : a){
	for(auto &col : row){
		col = cnt++;
	}
}
原因解释：
这里的&row是对原二维数组的引用即别名，如果没有&，则会发生内存拷贝，会把一行拷贝出来，row为新的一行的首地址。
```

类型别名简化多维数组的指针：

```
using int_array = int[4];	新标准下的类型别名声明
typedef int int_array[4];	等价的typedef声明
```

##### 求值顺序

```
int i = f1() + f2();	//先求f1还是f2是未知的
int i = 0;
cout << i << " " << ++i <<endl;
这一句先打印还是先++，顺序也是不可预知的
```

==建议：除非必须，否则不用递增递减运算符的后置位版本==

原因是：前置版本的的递增运算符避免了不必要的工作，它把值加1后直接返回，后置版本的还需存一下未修改的值，浪费！！相对于复杂的迭代器类型，消耗的性能更大，建议养成使用前置版本的习惯。

##### sizeof 运算符

sizeof 运算符返回一条表达式或一个类型名字所占的字节数；

sizeof 运算符满足结合律，其所得值是一个size_t类型的常量表达式：

```
sizeof(type)
sizeof expr
```

使用注意
```

        int* a =new int[5]{0};
         int arr2[] = {1,3,4,5};
         cout<<"sizeof(arr): "<<sizeof(a)<<endl;   //sizeof(arr): 8
        cout<<"sizeof(arr2): "<<sizeof(arr2)<<endl;  //sizeof(arr2): 16
```
？？？sizeof 数组、string 、vector......



### stack 与 queue
```

stack<int> s; //先进后出
	auto a=s.top(); //返回队首元素的值，但不删除该元素
	s.pop();//删除队列首元素但不返回其值

queue<int> q; //先进先出
	auto a=q.front(); //返回队首元素的值，但不删除该元素
	q.pop();//删除队列首元素但不返回其值
```
empty() 堆栈为空则返回真

pop() 移除栈顶元素

push() 在栈顶增加元素

size() 返回栈中元素数目

top() 返回栈顶元素

#### 神奇的remove() 函数
remove() 算法由定义在 algorithm 头文件中的模板生成，它可以删除匹配特定值的一段元素。例如：
```
vector<std::string> words { "one", "none","some", "all","all","all", "none"};
auto iter = std::remove(std::begin(words), std::end(words), "all"); 
//words: {"one", "none", "some", "most", "many", "none", "", "", ""}

words.erase(iter, words.end()); 
//words: {"one", "none", "some", "most", "many", "none"}

vector<std::string> words2;
for(auto i = words.begin(); i != iter; i++) 
  {cout<<*i; words2.push_back(*i);} //words2: {"one", "none", "some", "most", "many", "none"}
```

实例，对array操作，remove掉20
```
int myints[] = {10,20,30,30,20,10,10,20};      // 10 20 30 30 20 10 10 20
int* pbegin = myints;                          // ^
int* pend = myints+sizeof(myints)/sizeof(int); // ^                       ^

pend = std::remove (pbegin, pend, 20);         // 10 30 30 10 10 ?  ?  ?

std::cout << "range contains:";
  for (int* p=pbegin; p!=pend; ++p)
    std::cout << ' ' << *p;
```
输出： range contains: 10 30 30 10 10
注意 vector size 任然为 8， 但10 后面有一个it 指向 vector.end()所以到10结束输出。

为了摆脱这些多余的元素，可以使用成员函数 erase()。remove() 返回的迭代器可以这样使用：
```
words.erase(iter, std::end(words));//Remove surplus elements
```

### unordered_set
unordered_set基于哈希表
遍历：
```
       unordered_set<int> set;
        for (int i=0; i<list.size(); i++){
            set.insert(list[i]);
        }
        for (unordered_set<int>::iterator i = set.begin(); i != set.end(); i++) {
            cout << *i << endl;
        }
```


operator

```
  std::unordered_set<std::string> first;                                // empty
  std::unordered_set<std::string> second ( {"red","green","blue"} );    // init list
  std::unordered_set<std::string> third ( {"orange","pink","yellow"} ); // init list
  std::unordered_set<std::string> fourth ( second );                    // copy
  std::unordered_set<std::string> fifth ( cmerge(third,fourth) );       // move
  std::unordered_set<std::string> sixth ( fifth.begin(), fifth.end() ); // range

  std::cout << "sixth contains:";
  for (const std::string& x: sixth) std::cout << " " << x;
  std::cout << std::endl;

```
输出： sixth contains: pink yellow red green orange blue


批量添加元素
```
wordList: {"hot", "dot", "dog", "lot", "log", "cog"}

unordered_set<string> dict(wordList.begin(), wordList.end()); // fast!

 //dict: {[0] = "cog", [1] = "log", [2] = "lot", [3] = "dog", [4] = "dot", [5] = "hot"}
```

添加新的元素(注意无法插入相同元素)
```
c.insert("dddd")：向容器添加元素”dddd"
a.insert({ "aaa","bbbb","cccc" })：向容器添加元素"aaa","bbbb","cccc"
a.insert(b.begin(), b.end())：b是一个存储着和a相同类型元素的向量，可将b中所有元素添加到a中 
```

#### 字符串、向量和数组

##### using申明

- 当我们使用名字cin时，从命名空间std获取它

```
格式：using namespace::name;
例子：using std::cin;
```

##### string对象

初始化string的方式：

```
直接初始化：（推荐）
string s1;
string s2(s1);
string s3("value");
string s4(10, 'c'); //"cccccccccc"
拷贝初始化：
string s2 = s1;
string s3 = "value";
string s4 = string(10, 'c');
```

##### string对象操作

```
os<<s			将s写到输出流os中，返回os
is>>s			从is中读取字符串赋值给s，字符串以空白分割，返回is
s.empty()		判断字符串是否为空
getline(is,s)	从is中读取一行赋值给s，返回is
s.size()		返回s中字符个数
s1==s2			判断字符是否完全一样，大小写敏感
<,<=,>,>=		字典序比较，大小写敏感
```

cin读取字符串时，会自动忽略开头的空白（空格符、换行符、制表符），直到遇到下一个空白为止。

getline读取一行，直到遇到换行符为止，注意读取到的string不含换行符

切记：==字符串字面值和string是不同类型==

所以字符串字面值不能直接相加，必须要确保加法运算符（+）的两侧至少有一个是string。

```
string s5 = "hello" + ", ";			//错误：两个运算对象都不是string
string s6 = s1 + ", " + "world";	//正确
string s7 = "hello" + ", " + s2;	//错误：不能把字面值直接相加
```

##### cctype头文件中的函数

```
isalnum(c)	当c是字母或数字时为真
isalpha(c)	当c是字母时为真
iscntrl(c)	当c是控制字符时为真
isdigit(c)	当c是数字时为真
isgraph(c)	当c不是空格但可打印时为真
islower(c)	当c是小写字母时为真
isprint(c)	当c是可打印字符时为真
ispunct(c)	当c是标点符号时为真
isspace(c)	当c是空白时为真
issuper(c)	当c是大写字母时为真
isxdigit(c)	当c是十六进制数字时为真
tolower(c)	转小写
toupper(c)	转大写
```

字符串遍历：for(auto c : str)

如果想要改变string中字符值，须把循环变量定义成引用类型：for (auto &c : str) c = toipper(c);

string取下标时，返回值是该位置上字符的引用。

##### vector

```
vector<T> v1
vector<T> v2(v1) //v2中包含v1所有元素副本
vector<T> v2 = v1 //等价于v2(v1)
vector<T> v3(n, val) //包含n个重复元素
vector<T> v4(n) //包含了n个默认初始化元素（如果T为int，则默认初始化元素为0）
vector<T> v5{a,b,c...}//直接初始化
vector<T> v5={a,b,c...}//拷贝初始化

//二维vector
vector<vector<int>> res(r, vector<int>(r)) ；
```


构造函数
语法: 
```
  vector();
  vector( size_type num, const TYPE &val );
  vector( const vector &from );
  vector( input_iterator start, input_iterator end );
C++ Vectors可以使用以下任意一种参数方式构造: 
无参数 - 构造一个空的vector, 
数量(num)和值(val) - 构造一个初始放入num个值为val的元素的Vector 
vector(from) - 构造一个与vector from 相同的vector 
迭代器(start)和迭代器(end) - 构造一个初始值为[start,end)区间元素的Vector(注:半开区间). 
举例,下面这个实例构造了一个包含5个值为42的元素的Vector
vector<int> v1( 5, 42 );
```

```
注意以下初始化时对的：
vector<string> v7{10}			//有10个默认初始化元素
vector<string> v8{10, "hi"} 	//有10个值为hi的元素

如果初始化时使用了花括号但提供的值又不能用来列表初始化，此时会考虑用这样的值来构造vector对象
```

```
v.empty()		不含任何元素时返回真
v.size()		返回元素个数
v.push_back()	向末尾添加元素
v[n]			返回第n个位置上元素的引用
v1 = {a,b,c...}	用列表中元素拷贝替换v1中元素
v1 == v2		当且仅当元素数量相等和对应位置元素相同
<,<=,>,>=		以字典顺序进行比较
```

如果想改变vector中元素值，用引用：

```
for(auto &i : v) i *= i;
```

要使用size_type，需要首先指定定义类型：

```
vector<int>::size_type	正确
vector::size_type		错误
```

通过使用 vector 的成员函数 clear() 来删除所有的元素。例如：
```
std::vector<int> data(100, 99);// Contains 100 elements initialized to 99
data.clear(); // Remove all elements
```
第二条语句移除了所有的元素，因此大小变为 0，因为这个操作并没有改变容器的容量，所以容量还是 100。

使用成员函数 erase() 来删除容器中的一个或多个元素：
```
// erase the 6th element
  myvector.erase (myvector.begin()+5);

// erase the first 3 elements:
myvector.erase (myvector.begin(),myvector.begin()+3);
```

##### 迭代器

```
迭代器的迭代一般用==或!=而不用<、>，因为只有string和vector等一些标准库类型有下标运算，而所有的标准库容器的迭代器都定义了==和!=
例如：
for(auto it = s.begin(); it != s.end(); ++it){
	*it = toupper(*it);
}
```

##### 迭代器类型

```
vector<int>::iterator it;	能读写vector<int>中的元素
string::iterator it2;			能读写string中的元素
vector<int>::const_iterator it3;	只能读元素，不能写
string::const_iterator it4;			只能读元素，不能写
```

- 如果vector对象或string对象是常量，则只能用const_iterator；
- 不是常量则都能用
- 如果对象是常量，begin和end返回的是const_iterator，如果对象不是常量则返回iterator

```
(*it).empty();	解引用it，然后调用结果对象的empty成员，等价于it->empty()
*it.empty();	错误！
```

#### 函数

##### 传引用参数



```
int reset(int &a){
	a = 100;
}
int main(){
	int i = -3;
	reset(i);
	cout << i <<endl;//打印出：100
}

```

##### 使用引用避免拷贝

使用引用作为函数形参时，不会发生拷贝，可用于大对象，大字符串。

##### 使用引用形参返回额外信息

函数只能返回一个值，想返回多个值时，可以定义引用形参，这样，引用形参不返回也能传递额外信息。

##### const形参和实参

和其他初始化过程一样，当实参初始化形参时，会忽略掉顶层const。换句话说，形参的顶层const会被忽略掉。

当形参有顶层const时，传给它常量对象或非常量对象都行。

```
const int ci = 42;	//不能改变ci，因为它是顶层const
int i = ci;	//拷贝ci时，忽略了它的顶层const
void func(const int i){} //func能读取i，但不能向i写值
```

我们可以使用非常量初始化一个底层const对象，但反过来不行。

```
int &r4 = 42; 错误：不能用字面值初始化一个非常量引用
```

##### 一维数组传递

```
void swap(int arry[], int i, int j){}
void swap(int *array, int i, int j){}
```

##### 多维数组传递

```
//matrix指向数组的首元素，该数组的元素是由10个整数构成的数组
void print(int (*matrix)[10], int rowSize){}
再次强调：
int *matrix[10];	10个指针构成的数组
int (*matrix)[10];	指向含有10个整数的数组的指针
//等价定义
void print(int matrix[][10], int rowSize){}
```

##### 含有可变形参的函数

如果所有实参类型相同，可以传递一个名为initializer_list的标准库类型；如果实参类型不同，可以用省略符；

initializer_list形参：？？？

和vector不一样的是，initializer_list对象中的元素永远是常量值，我们无法改变initializer_list对象中的元素值。

省略符形参只出现在形参列表最后的一个位置：

```
void foo(param, ...);
void foo(...);
```

##### 值是如何被返回的

返回一个值的方式和初始化一个变量或形参的方式完全一样；

如果返回的是字面值，则返回值将被拷贝到调用点；

如果返回值是引用，则是它所引对象的一个别名；

不要返回局部对象的引用或指针！！！函数终止异味着局部变量的引用将指向不再有效的区域；

C++ 不支持在函数外返回局部变量的地址，除非定义局部变量为 static 变量。

**引用返回左值**：调用一个返回引用的函数得到左值，我们能为返回类型是非常量引用的函数结果赋值：

```
char &get_val(string &str, string::size_type ix){
	return str[ix];
}
void main(){
	string s("a value");
	get_val(s, 0) = 'A';	//为引用赋值
}
```

**列表初始化返回值**：C++11新标准规定，函数可以返回花括号包围的值的列表，用来对表示hash返回值的临时量进行初始化，如果列表为空，临时量执行初始化：

```
vector<string> process(){
	if(expected.empty()) 
		return {};
	else if(expected == actual)
		return {"fffff", "asdsd"};
	else
		return {"ffff", expected, actual};
}
```

**申明一个返回数组指针的函数**：？？？

```
int (*func(int i))[10];表示数组中的元素是int类型
```

**使用尾置返回类型**：尾置返回类型跟在形参列表后面并以一个->符号开头

```
//func接受一个int类型的实参，返回一个指针，该指针指向含有10个整数的数组
auto func(int i) -> int(*)[10];
```

？？？

使用decltype**:？？？

##### 函数重载

**const_cast：？？？

##### 默认实参

调用含有默认实参的函数时，可以包含该实参，也可省略该实参；

我们可以为一个或多个形参定义默认值，一旦某个形参被赋予了默认值，它后面的所有形参都必须有默认值。

##### 內联函数可避免函数调用的开销

```
inline const string &
shorterString(const string &s1, const string &s2){
	return s1.size() <= s2.size() ? s1 : s2;
}
cout < shorterString(s1, s2) << endl;
在编译过程中展开成类似于下面的形式：
cout << (s1.size() < s2.size() ? s1 : s2) << endl;
```

##### constexpr函数

函数的返回值类型及所有的形参类型都得是字面值类型。而且函数体中必须有且只有一条return语句：`constexpr int new_sz() {return 42;}`

constexpr函数不一定返回常量表达式。

##### assert预处理宏

预处理宏其实是一个预处理变量，它的行为有点类似于內联函数，`assert（expr)`，首先对expr求值，如果为假（即0），输出信息并终止程序，为真，什么都不做。

assert无需using声明，直接assert(expr)，而不是std::assert(expr)。

##### NDEBUG预处理

？？？

##### 函数指针

```
bool (*pf)(cosnt string &, const string &);//未初始化、
//注意区分：*pf两端括号不可少，下面为返回bool指针的函数
bool *pf(cosnt string &, const string &);
```

当我们把一个函数当做一个值来使用时，该函数自动转换成指针；

```
1.函数指针赋值：
pf = lengthCompare;	//pf指向名为lengthCompare的函数
pf = &lengthCompare; //等价的赋值语句，&可选
2.直接使用指向函数的指针调用该函数，无须提前解引用指针：
bool b1 = pf("hello", "good");	//调动lengthCompare函数
bool b2 = (*pf)("hello", "good");//等价于上面
```

##### 函数指针形参

```
//第三个形参是函数类型，它会自动转换成指向函数的指针
void useBigger(const string &s1, const string &s2,
	bool pf(const string &, cosnt string &));
//等价的声明：显示第将形参定义为指向函数的指针
void useBigger(cosnt string &s1, const string &s2,
	bool (*pf)(const string &, const string &));
```

我们可以直接把函数作为实参使用，此时会自动转为指针：

```
useBigger(s1, s2, lentgCompare);
```

用类型别名和decltype来简化函数指针：

```
//Func 和 Func2是函数类型

bool Func(const string&, const string &);
typedef decltype(lengthCompare) Func2;//等价类型
//FuncP 和 Func2是指向函数的指针
typedef bool (*FuncP)(const string&, const string&);
typedef decltype(lengthCompare) *Func2;//等价类型
```

我们必须把返回类型写成指针形式，编译器不会自动地将函数返回类型当成对应的指针类型处理。想要申明一个返回函数指针的函数，最简单的办法是使用类型别名：

```
using F = int(int*, int);	//F是函数类型，不是指针
using PF = int(*)(int*, int);//PF是指针类型
```

我们必须显示地将返回类型指定为指针：

```
PF f1(int);	//正确
F f1(int);	//错误
F *f1(int);	//正确
int (*f1(int))(int*, int);//直接申明f1
按照由内到外的顺序解读这条语句：f1有形参列表，所以f1是个函数，f1前面有*，所以f1返回一个指针，指针的类型本身也包含形参列表，因此指针指向函数，该函数返回类型是int。
我们还可以使用尾置返回类型的方式：
auto f1(int) -> int(*)(int*, int);
```

#### 类

使用class和struct定义类唯一区别就是默认访问权限：

struct：定义在第一个访问说明符之前的成员是public的

clasee：定义在第一个访问说明符之前的成员是private的

类可以允许其他类或函数访问它的非公有成员，方法是令其他类或者函数成为它的友元

类还可以把其他的类定义成友元，也可以把其他类的成员函数定义为友元，此外，友元函数能定义在类的内部，这样的函数是隐式內联的。

友元类的成员函数可以访问此类包括非公有成员在内的所有成员，但友元关系不存在传递性。

#### IO库

##### 查询流的状态：

badbit：表示系统级错误，如不可恢复的读写错误；在发生可恢复错误后，failbit被置位，如期望读取数值缺读出一个字符等错误。这种问题通常是可修正的，如果到达文件结束位置，eofbit和failbit都会被置位。goodbit的值为0，表示流未发生错误。如果badbit、failbit、eofbit任意一个被置位，则检测流状态的条件会失败。

##### 管理输出缓冲区

每个输出流斗殴管理一个缓冲区，用来保存程序读写数据。`os << "sdaff";`这句文本串可能被打印出来也可能被操作系统保存在缓冲区中，随后再打印。

##### 数据真正写入输出设备或文件

- 程序正常结束；
- 缓冲区满；
- 使用操纵符如endl来显示刷新；
- 使用操纵符unitbuf设置流的呢部状态，来清空缓冲区。默认情况下，对cerr是设置unitbuf的，因此写到cerr的内容都是立即刷新的。
- 一个输出流被关联到另一个流，这种情况下，读写被关联的流时，关联到的流的缓冲区会被刷新。默认情况下，cin和cerr都关联到了cout。因此，读cin或写cerr都会导致cout的缓冲区被刷新。

IO中除了endl还有两个类似的操纵符：flush和ends。例如：flush刷新缓冲区，但不输出任何额外字符；ends向缓冲区插入一个空字符，然后刷新缓冲区。

```
cout << "hi" << endl;
cout << "hi" << flush;	
cout << "hi" << ends;
```

**unitbuf操纵符**：它告诉流在接下来的每次写操作之后斗殴进行一次flush操作。而nounitbuf操纵符则重置流。

```
cout << unitbuf;//所有输出操作后都会立即刷新到缓冲区
cout << nounitbuf;//回到正常的缓冲方式
```

警告：如果程序异常终止（奔溃），输出缓冲区是不会被刷新的。

#### 泛型算法

##### lambda表达式

`[捕获列表](参数列表) -> return type {function body}`

可以忽略参数列表和返回类型，但是必须永远包含捕获列表和函数体，如果lambda的函数体包含任何单一return语句之外的内容，且未指定返回类型，则返回void。

lambda通过将局部变量包含在其捕获列表中来指出将会使用这些变量。

一个lambda只有在其捕获列表中捕获一个它所在函数中的局部变量，才能在哈数提中使用该变量。捕获列表只用于局部非static变量，lambda变量可以直接使用局部static变量和在它所在函数之外申明的名字。

**隐式捕获**：为了指示编译器推断捕获列表，应该在捕获列表中写一个&或=。&告诉编译器采用捕获引用方式，=则表示采用值捕获方式。

当我们混合使用隐式捕获和显示捕获时，捕获列表中的第一个元素鄙俗是&或=。

```
//os隐式捕获，引用捕获方式;c显示捕获，值捕获方式
for_each(words.begin(),words.end(),
	[&,c](const string &s)) {os << s << c;};
//os显示捕获，引用捕获方式；c隐式捕获，值获取方式；
for_each(words.begin(),words.end(),
	[=,&os](const string &s)) {os << s << c;};
```



#### 动态内存

**静态内存**用来保存局部static对象、类static数据成员以及定义在任何函数外的变量。

**栈内存**用来保存定义在函数内的非static对象。

分配在静态内存或栈内存中的对象由编译器自动创建或销毁。

对于栈对象，仅在其定义的程序块运行时才存在；static对象在使用之前分配，在程序结束时销毁；

程序用堆来存储**动态分配**的对象；

**new**：在动态内存中未对象分配空间并返回一个指向该对象的指针；

**delete**：接受一个动态对象的指针，销毁该对象，并释放与之相关的内存。

##### 智能指针

智能指针的行为类似于常规指针。重要的区别是它负责自动释放所指向的对象。分为：

- shared_ptr：允许多个指针指向同一对象；
- unique_ptr：独占所指向的对象；
- weak_ptr：弱引用，指向shared_ptr所管理的对象；

这三类都定义在memory头文件中

```
shared_ptr<string> p1;
shared_ptr<list<int>> p2;
```

**注意**：

- 不要混合使用普通指针和智能指针
- 智能指针类型定义了一个名为get的函数，它返回一个内置指针，指向智能指针管理的对象。此函数只为这种情况设计：我们需要向不能使用智能指针的代码传递一个内置指针。使用get返回的指针的代码不能delete此指针。
- 永远不要用get初始化另一个智能指针或者为另一个智能指针赋值。这样会导致其中一个智能指针释放内存后，另一个智能指针变成空悬指针，使用时发生未定义行为，且销毁时，这块内存被第二次delete。

##### 智能指针和异常

如果使用智能指针，即使程序块过早结束，智能指针类也能确保在内存不在需要时将其释放。

创建shared_ptr时，可以传递一个（可选）指向删除器函数的参数来代替delete。

```
void end_connection(connection *p) {disconnection(*p)}
shared_ptr<connection> p(&c, end_connection);
```

为了正确使用智能指针，我们必须坚持一些基本规范：

- 不使用相同的内置指针（或reset）多个智能指针；
- 不delete get() 返回的指针；
- 不使用get()初始化或reset另一个智能指针；
- 如果你使用get()返回的指针，记住当最后一个对应的智能指针销毁后，你的指针就变为无效了；
- 如果你使用的智能指针管理的不是new分配的内存，记住传递给它一个删除器。

##### unique_ptr

某个时刻只能有一个unique_ptr指向一个给定对象，初始化unique_ptr必须采用直接初始化形式，unique_ptr不支持普通的拷贝或赋值操作。

##### week_ptr

week_ptr是一种不控制所指向对象生存期的智能指针，它指向由一个shared_ptr管理的对象。将一个week_ptr绑定到一个shared_ptr不会改变shared_ptr的引用计数。一旦最后一个指向对象的shared_ptr被销毁，对象就会被释放。即使有week_ptr指向对象，对象也还是会被释放。

我们创建一个week_ptr时要用shared_ptr来初始化它；

由于对象可能不存在，所以我们不能直接使用week_ptr来访问对象，而必须调用lock来检查week_ptr指向的对象是否存在；如果存在，lock返回一个指向共享对象的shared_ptr

```
if(shared_ptr<int> np = wp.lock()){}
```

##### 直接管理内存

如果我们提供了一个括号包围的初始化器，就可以使用auto从此初始化器来推断我们想要分配的对象的类型。

```
auto p1 = new auto(obj); //p指向一个与obj类型相同的对象，该对象用obj进行初始化；
```

默认情况下，如果new不能分配所要求的空间，它会抛出一个类型为bad_alloc的异常。

##### 动态数组

我们通常称new T[ ]分配的内存为“动态数组”，使用new分配一个数组时，并未得到一个数组类型的对象，而是得到一个数组元素类型的指针。因此不能对动态数组调用begin 或end，也不能用for语句来处理动态数组中的元素。

**初始化动态分配对象的数组**：

```
int *pia = new int[10];		//10个未初始化的
int *pia2 = new int[10]();//10个初始化为0的
```

当我们释放一个指向数组的指针时，空括号是必需的；如果我们在delete一个指向数组的指针时忽略了方括号（或者在delete一个只下过单一对象的指针时使用了方括号），其行为是未定义的。

```
delete p;//p必须指向一个动态分配的对象或为空；
delelte [] pa;//pa必需指向一个动态分配的数组或为空；
```

##### allocator类

STL allocator定义在头文件memory中，它将内存分配和对象构造分离开来，它提供一种类型感知的内存分配方法，它分配的内存是原始的、未构造的。为了使用allocator返回的内存，我们必须用construct构造对象。使用未构造的内存，其行为是未定义的。当我们用完对象后，必须对每个构造的元素调用destroy来销毁对象。一旦对象被销毁后，就可以重新使用这部分内存来保存其他string，也可以将其归还给系统。释放内存通过调用deallocate来完成。

#### 拷贝、赋值与销毁

##### 拷贝构造函数

```
class Foo{
public:
	Foo();			//默认构造函数
	Foo(const Foo&);//拷贝构造函数
}
```

拷贝构造函数的第一个参数必须是一个引用类型；

#####深拷贝和浅拷贝
如果一个类选用自定义析构函数，几乎可以肯定它也需要自定义拷贝赋值运算符和拷贝构造函数。需要拷贝操作的类也需要赋值操作。
遇到指针的时候，他会知道new出来一块新的内存，然后把原来指针指向的值拿过来，这样才是真正的完成了克隆体和原来的物体的完美分离
编译器等，所以默认的行为都是浅拷贝。
```
B* b3 = b2. The pointers will point to the same location.
B* b3 = new B(*b2); is deep copy
```

#### 面向对象程序设计

基类通常都应该定义一个虚析构函数，即使该函数不执行任何实际操作也是如此。

在C++中，基类必须将它的两种成员函数区分开来：一种是基类希望其派生类进行覆盖的函数。另一种是希望派生类直接继承而不要改变的函数。对于第一种，基类通常将其定义为虚函数。当我们使用指针或引用调用虚函数时，该调用将被动态绑定。

关键字virtual只能出现在类内部的声明语句之前而不能用于类外部的函数定义。基类中的虚函数，在派生类中隐式的也是虚函数。

成员函数如果没有被声明为虚函数，则其解析过程将发生在编译而非运行时。

派生类能访问基类的公有成员，而不能访问私有成员，派生类有权访问基类的protected成员。

派生类必须通过**类派生列表**明确指定是从哪些基类继承而来。派生列表的形式是：冒号+public/protected/private + 基类列表；访问说明符的作用是控制派生类从基类继承而来的成员是否对派生用户可见；

派生类也必须使用基类的构造函数来初始化它的基类部分；

派生类可以访问基类的公共成员和保护成员；

不论从基类中派生出多少派生类，对于静态成员来说都只存在唯一实例。

防止继承的发生——用关键字final；final修饰的类不能作为基类。

##### 类型转换与继承

**静态类型**：在编译时总是已知的；

**动态类型**：直到运行时才可知；

**不存在从基类向派生类的隐式类型转换**：之所以存在派生类向基类的类型转换是因为每个派生类对象都包含一个基类部分，而基类的引用或指针可以绑定到该基类部分上。

**关键概念：存在继承关系的类型之间的转换规则**

- 从派生类向基类的类型转换只对指针或引用类型有效；
- 基类向派生类不存在隐式类型转换；
- 派生类向基类的类型转换也可能会由于访问受限而变得不可行。

##### 虚函数

**对虚函数的调用可能在运行时才被解析**：

当某个虚函数的通过指针或引用调用时，编译器产生的代码直到运行时才能确定应该调用哪个版本的函数。被调用的函数时与绑定到指针或引用上的对象的动态类型相匹配的那个。

一旦某个函数被声明为虚函数，则在所有的派生类中它都是虚函数，所以无需在派生类中再一次使用virtual。

使用override关键字来标记某个函数是覆盖已经存在的虚函数；

使用final关键字来标记禁止覆盖该函数；

##### 纯虚函数、抽象基类

一个纯虚函数无需定义，我们通过在函数体位置书写=0就可将一个虚函数说明为纯虚函数。其中=0只能出现在类内部的虚函数声明语句处：

```
double net_price(std::size_t) const = 0;
```

含有纯虚函数的类是抽象基类，不能直接创建一个抽象基类的对象。

派生类构造函数只初始化它的直接基类。

##### 改变个别成员的可访问性

通过在类内部使用using声明的语句，我们可以将该类的直接或间接基类中的任何可访问成员标记出来。

```
public:
	using Base::size;
private:
	using Base::n;
```

默认情况下，使用class定义的派生类是私有继承的；使用struct定义的派生类是公有继承的；

**名字查找先于类型检查**：

如果派生类的成员与基类的某个成员同名，则派生类将在其作用域内隐藏该基类成员。即使派生类成员和基类成员的形参列表不一致，基类成员也仍然会被隐藏。

##### 虚析构函数

基类通常应该定义一个虚析构函数，这样我们就能动态分配继承体系中的对象了。

如果基类的析构函数不是虚函数，则delete一个指向派生；类对象的基类指针将产生未定义行为。

### 笔试用输入数据模板
##### 类型一：两行未知数量
1 2 3 4 ...

5 6 7 8 ...

```
vector<int> shua,shub;
int main(){
	string line;
	getline(cin,line);
	stringstream ss1(line);
	int s,lena=0,lenb=0;
	while(ss1 >> s){
		shua.push_back(s);
	}
	getline(cin,line);
	stringstream ss2(line);
	while(ss2>>s){
		shub.push_back(s);
	}

//shua 第一个数组 shub第二个数组
return 0;
}
```
##### 类型二：一行字符串有空格
aBC DEF....
```
const int maxn=1030;
string str[maxn];
 
int main(){
	string s;
	string line;
	getline(cin,line);
	stringstream ss1(line);
	int i=0;
	while(ss1>>s){
	  str[i++]=s;
	}
//str 为字符串数组
return 0;
}
```

##### 类型三：多行多参数
5 6 //任务数量（节点数N） 依赖关系（边数M）

1 2 1 1 1  // N个数表示该任务处理时间

1 2 //M 行表示任务依赖关系

1 3

1 4 

...
```
struct EdgeList
{
	int to,next;
}e[110000];
int n,m,in[11000];
pair<int,int> a[11000];

void insert(const int &x,const int &y)
{
	in[y]++;
	e[++cnt].to=y;
	e[cnt].next=p[x];
	p[x]=cnt;
}

int main(){
cin >> n >> m;
	for(int i=1;i<=n;++i)
	{
		cin >> a[i].first; // 所需时间
		a[i].second=i; //当前节点
	}
	for(int i=1;i<=m;++i)
	{
		int x,y;
		cin >> x >> y;
		insert(x,y);
	}
}
```
