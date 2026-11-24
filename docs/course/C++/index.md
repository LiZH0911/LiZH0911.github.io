# C++

相关链接：

- [一口气速通《C++程序设计》](https://www.bilibili.com/video/BV1eunh6FE7e/?spm_id_from=333.337.search-card.all.click&vd_source=46f99c7c1ed609a31f70615a4551767f)
- [C++ 那些事](https://light-city.github.io/stories_things/zh/)

## 一、程序基础

---

### 1.1 **编译流程与开发环境**

C++ 源文件扩展名一般为 `.cpp`。

C++ 是**编译型语言**。C++ 源码要经过四步才能变成可执行程序：

1. 预处理（展开宏和头文件）
2. 编译（翻译成汇编/机器码）
3. 汇编
4. 链接（拼接库和入口）

![C++是怎么跑起来的.png](images%2FC%2B%2B%E6%98%AF%E6%80%8E%E4%B9%88%E8%B7%91%E8%B5%B7%E6%9D%A5%E7%9A%84.png)

> 直观理解：`.cpp` 只是给人看的文本，机器看不懂。编译器相当于翻译官，把整本"说明书"翻译成机器码，链接器再把它拼成能双击运行的程序。

**常用编译器**：GCC、Clang、微软 MSVC。

- 使用命令行进行编译，还可指定标准版本：

```bash
g++ main.cpp -o main
g++ -std=c++17 main.cpp -o main
```

或者使用**集成开发环境（IDE）**，如 Visual Studio、VS code、Dev-C++、CLion 等，点一下按钮，**编译和运行一次完成**。

分清这三类错误的位置：

* 语法错误：在编译阶段暴露
* 链接错误：说明"有声明没定义"（如模板没写全、缺库）
* 运行时错误：程序跑起来崩溃

---

### 1.2 **第一个 C++ 程序**

#### **1.2.1 main 函数与 cout / cin**

```C++
#include <iostream>
int main() {
    std::cout << "Hello, C++" << std::endl;
    int x;
    std::cin >> x;
    return 0;
}
```

其中：

- `#include <iostream>` 引入输入输出流库
- `int main()` 是程序唯一入口，有且只有一个；`main` 是特殊函数名，不能改名也不能重载，必须返回 `int`，不能写 `void main`
- `std::cout` 标准输出、`std::cin` 标准输入（多个输入用空格分开）、`std::endl` 换行
- `std` 是标准命名空间，`using namespace std` 只在写小程序或考试时用，写进头文件会污染全局命名空间
- 语句以分号 `;` 结尾

#### **1.2.2 标识符与命名规则**

标识符由字母、数字、下划线组成。

**命名规则**：

* 不能以数字开头
* 不能与关键字重名
* 区分大小写

> 避免以下划线开头的名字（可能与系统保留名冲突）

#### **1.2.3 注释与语句结构**

- `//` 单行注释；`/* ... */` 块注释（不能嵌套）
- 每条语句以分号 `;` 结束
- 大括号 `{}` 表示语句块/函数体

---

## 二、数据类型与运算

---

### 2.1 **基本类型与字节大小**

**C++ 是静态类型语言**：变量类型在定义时就要写明类型，之后不能改变。

常见基本类型：

* 整型： `char`、`short`、`int`、`long long`
* 浮点型： `float`、`double`
* 布尔型： `bool`。

在 64 位平台下的字节数：

| 类型 | 字节 | 典型范围 |
|---|---|---|
| `char` | 1 | -128 ~ 127 |
| `short` | 2 | -32768 ~ 32767 |
| `int` | 4 | 约 ±21 亿 |
| `long long` | 8 | 很大 |
| `float` | 4 | 约 6~7 位有效数字 |
| `double` | 8 | 约 15~16 位有效数字 |
| `bool` | 1 | `true` / `false` |
| `long` (Windows / Linux) | 4 / 8 | 平台相关 |

> long 在 Windows 是 4 字节、Linux 是 8 字节，属于平台相关考点

实际大小查看：

```C++
#include <iostream>
int main() {
    std::cout << sizeof(int) << std::endl;
    return 0;
}
```

---

### 2.2 **unsigned 无符号数与溢出回绕**

`unsigned` 仅表示非负数，n 位无符号数范围为 `0 ~ 2^n - 1`，例如 `unsigned int` 的范围为 0 ~ 42 亿。

* **无符号数超出范围** → 溢出，会回绕（如 `unsigned char` 255 + 1 = 0）
* **有符号数超出范围** → 未定义行为，结果不可预测。

```C++
#include <iostream>
#include <climits>

int main() {
    unsigned char a = 255;
    std::cout << a + 1 << '\n';        // 256（unsigned char 与 int 相加，提升为 int，不回绕）
    std::cout << (unsigned char)(a + 1) << '\n'; // 0，回绕

    int x = INT_MAX;
    std::cout << x + 1 << '\n';         // 未定义行为
```

---

### 2.3 **char 与 ASCII 码**

* `char` 类型的字符常量底层是 **1 字节整数**，可参与算术
* 常用 ASCII 码: `'A'` 为 `65`、`'a'` 为 `97`、`'0'` 为 `48`；**大小写字母相差 32**
* 单引号包的是字符（占 1 字节），双引号包的是字符串（自带结尾 `\0`，如 `"A"` 实际占 2 字节）
    * `sizeof('A')` 与 `sizeof("A")` 不同

典型应用：

* **大小写转换**：`ch + 32` 把大写变小写，例如 `'A'+32` 为 `'a'`
* **判断数字字符**：`'0' <= ch && ch <= '9'`

---

### 2.4 **整型/浮点常量与进制**

* 十进制直接写
* **八进制**以 `0` 开头。例如 八进制数 `012` 等于十进制的 `10`
* **十六进制**以 `0x` 开头。例如 十六进制数 `0x2A` 等于十进制的 `42`
* **浮点常量**默认 `double`，加 `f`/`F` 后缀才是 `float`。例如 `float f = 3.14f;`
* **科学计数法**如 `1e3` 为 `1000`。例如 `double d = 1.5e-2;`
* 布尔 `true`/`false` 参与输出时显示为 `1`/`0`。例如 `bool b = true; std::cout << b; // 输出 1`

---

### 2.5 **符号常量: define / const / constexpr**

- `#define PI 3.14` 是预处理阶段的宏替换（文本替换），无类型、无作用域、编译前替换；
- `const int PI = 3.14;` **有类型检查**、遵守作用域；
- `constexpr int SQ = 4 * 4;` 是**编译期求值的常量表达式**，用于数组大小、模板参数等必须编译期确定的场合。

> 注意：宏只是文本替换，`const` 是真正的变量；能用 `const`/`constexpr` 就不要用 `#define`（除了头文件保护）。`constexpr` 修饰的变量必须在编译期能算出值。

---

### 2.6 **类型转换：隐式转换、显式转换、auto**

- **隐式转换**：小类型向大类型提升（int → float → double），赋值/运算时自动发生，浮点转整型会截断小数。
    - 示例：`5/2` 的结果为 `2`，`5.0/2` 的结果为 `2.5`
- **显式转换**：推荐 `static_cast<T>(x);` 。例如 `int n = static_cast<int>(3.9); // 3, 截断`
- `auto` 让编译器自动推导类型，不产生转换。

---

### 2.7 **算术运算**

- **整数除法**：**向零截断**，例如 `5 / 2` = `2` ， `-5 / 2` = `-2` 。
- **取余**：**余数符号跟随被除数**。例如 `5 % 2` = `1` ， `-5 % 2` = `-1` ， `5 % -2` = `1` 。
    * **判断奇偶**：`x % 2 == 0` 为偶数；
    * **取某数的个位**：`n % 10`。
- **自增**：前置 `++i`（先加后用），后置 `i++`（先用后加）

> C++ 没有 `**` 运算符，即没有乘方运算符。乘方运算需要使用 `pow` 函数，定义在 `<cmath>` 头文件中。

---

### 2.8 **关系运算与逻辑运算**

* **关系运算符**：`==`、`!=`、`<`、`<=`、`>`、`>=`
    * 结果是 `bool`（`1`/`0`）
* **逻辑运算符**：`&&` 与、`||` 或、`!` 非
    * **短路求值**：`&&` 左边为假就不算右边，`||` 左边为真就不算右边

---

### 2.9 **位运算**

* `&` 按位与
* `|` 按位或
* `^` 按位异或
* `~` 按位取反
* `<<` 左移（乘 2）
* `>>` 右移（除 2）。

典型应用：

* **判断奇偶**：`x & 1 == 1`；
* **乘 2**：x << 1；
* **取低 4 位**：x & 0xF；
* 交换两数：a ^= b; b ^= a; a ^= b;

> `x << 1` 不改变 `x` 本身，要 `x = x << 1` 才生效；
> 
> `^` 是异或不是乘方。

---

### 2.10 **表达式运算符：三目 / 逗号 / sizeof**

* 三目：`条件 ? 真值 : 假值`
    * `int max = (a > b) ? a : b;`
* 逗号表达式整体取最后一个表达式的值
    * `int a = (1 + 2, 3 + 4, 5 + 6); // 执行过程：先算 1+2=3 (丢弃) -> 再算 3+4=7 (丢弃) -> 最后算 5+6=11。最终 a 的结果是 11。`
* `sizeof` 在编译期求对象/类型字节数
    * `int n = sizeof(arr) / sizeof(arr[0]); // 数组元素个数`

**易错提醒**：`sizeof` 是**单目运算符**不是函数，`sizeof(arr)` **对数组名返回整个数组大小**，但**数组作为函数参数退化成指针**后 `sizeof` 只返回指针大小（8），这是经典坑。

```C++
int arr[10] = {0}; 
// 一个 int 占 4 字节，10 个 int 就是 40 字节。
// 此时的 arr 就是数组本身。
std::cout << sizeof(arr); // 输出 40 
```

```C++
void printSize(int arr[]) {
    // 注意：这里的 arr[] 只是长得像数组，
    // 实际上在编译器眼里，它等同于 int* arr (一个指针变量)
    std::cout << sizeof(arr); 
}

int main() {
    int arr[10] = {0};
    printSize(arr); // 输出 8（在 64 位系统下）
}
```

---

### 2.11 **运算符优先级与结合性**

- **运算符优先级（从高到低）**：括号 `()` → 单目 (`++ -- * & ! ~ sizeof`) → 乘除 → 加减 → 移位 → 关系 → 相等 (`== !=`) → 位与 → 位异或 → 位或 → 逻辑与 → 逻辑或 → 三目 → 赋值 → 逗号。
- **结合性**：赋值右结合，其余多数左结合。

> 焚诀：加括号！

![运算符优先级与结合性.png](images%2F%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BC%98%E5%85%88%E7%BA%A7%E4%B8%8E%E7%BB%93%E5%90%88%E6%80%A7.png)

---

## 三、控制结构

### 3.1 **if / else**

```C++
if (条件) {语句;}
else if {语句;}
else {语句;}
```

**悬空 `else` 规则**：`else` 总是与最近的、尚未配对的 `if` 结合（不看缩进）

![悬空 else.png](images%2F%E6%82%AC%E7%A9%BA%20else.png)

---

### 3.2 **switch**

```C++
switch (day) {
    case 1: cout << "Mon"; break;
    case 2: cout << "Tue"; break;
    case 3: cout << "Wed"; break;
    case 4: cout << "Thu"; break;
    case 5: cout << "Fri"; break;
    case 6: cout << "Sat"; break;
    case 7: cout << "Sun"; break;
    default: cout << "?";
}
```

- `switch (整型表达式)` 按值跳转；
- `case` 值必须是**整型常量表达式**；`case` 里定义变量最好加 `{}` 隔离作用域；
- 漏写 `break` 会贯穿（继续执行下一个 `case`）；
- `default` 处理所有不匹配的情况。

---

### 3.3 **while / do-while / for**

- `while (条件){循环体;}` 先判断（可能执行 0 次）
- `do {循环体;} while (条件);` 先执行（至少执行 1 次）
- `for (初始化; 条件; 更新){循环体;}` 适合已知次数

> `while` 条件不更新会死循环；`do-while` 末尾的分号不能丢。

示例：计算 1 到 10 的和：

```C++
// while
int i = 1;
while (i <= 10) {
    sum += i;
    ++i;
}

// for
for (int i = 1; i <= 10; ++i) {
    sum += i;
}

// do-while
int i = 1;
do {
    sum += i;
    ++i;
} while (i <= 10);

```

---

### 3.4 **break / continue / 范围 for**

- `break` 立即退出当前循环/`switch`；
- `continue` 跳过本次循环剩余语句进入下一次。
- 范围 `for`（C++11）：`for (类型 变量 : 容器)` 自动遍历每个元素，是迭代器遍历的简写。


```C++
for (int x : v) cout << x; // 只读; 
for (int& x : v) x *= 2; // 加引用才能改原元素
```

> 范围 `for` 想修改元素必须用引用；范围 `for` 底层依赖 `begin/end`，遍历时不要修改容器大小（迭代器失效）。

---

## 四、数组与字符串

---

### 4.1 **一维数组**

- **声明数组**：`int a[5];` 
- **初始化数组**：`int a[5] = {1,2,3};`，未列出的元素自动补 0；
- **访问数组**：`a[4]`
- **元素个数**：`sizeof(a)/sizeof(a[0])`

注意事项：

- **数组下标从 0 开始**，在内存中**连续存放**
- **C++ 不检查越界（最危险的 bug 之一）**，越界读写是未定义行为（可能不报错但很危险）
- 数组不能整体赋值（`a = b` 非法）
- 数组名是常量地址不能自增/赋值
- 函数参数里数组退化为指针，`sizeof` 失效

---

### 4.2 **二维数组**

* **声明二维数组**：`int a[3][4];` 是 3 行 4 列，**在内存中按行存放**。
* **初始化**：`int a[2][3] = {{1,2,3},{4,5,6}};` 或平铺 `{1,2,3,4,5,6}`。
    * 省略规则：第一维可以省略，第二维（表示每行多长）不能省。例如 `int a[][4]` 合法，`int a[3][]` 非法。
* **访问二维数组**：`a[i][j]` 等价于 `*(*(a+i)+j)`。

二维数组求和示例：

```C++
for (int i = 0; i < 3; ++i){
    for (int j = 0; j < 4; ++j){
        sum += a[i][j];
    }
}
```

### 4.3 **C 风格字符串：以 \0 结尾的字符数组**

**C 风格字符串**：`char s[] = "hello";` 

- 实际是 6 字节（5 个字符 + 结尾 `\0`）
- `sizeof` 含 `\0`
- `#include <cstring>`
    - `strlen` 求长度，统计到 `\0` 为止（不含 `\0`）
    - `strcpy` 复制
    - `strcat` 拼接
    - `strcmp` 逐个字符比较（相等返回 0，前者大返回正数，前者小返回负数）

易错提醒：

- `strcpy` 和 `strcat` 不检查目标空间，**写越界**是常见的安全漏洞
- `gets` 已废弃禁用
- `cin >>` 读字符串遇空格就停（"hello world" 只读入 hello）
- `strcmp` 返回 0 表示相等（不是 1）
- `s1 == s2` 比较的是两个地址

```C++
#include <iostream>
#include <cstring>
using namespace std;

int main() {
    char s[] = "hello";              // 6 字节：'h','e','l','l','o','\0'
    cout << strlen(s) << endl;       // 5（不含 \0）
    cout << sizeof(s) << endl;       // 6（含 \0）

    char t[20];
    strcpy(t, s);                    // 复制 "hello"（不检查空间）
    strcat(t, " world");             // 拼接 → "hello world"
    cout << t << endl;               // hello world
    cout << strcmp(s, "hello") << endl; // 0 表示相等

    char name[20];
    cin >> name;                     // 遇空格停止，"hello world" 只读入 hello
    cout << name << endl;

    return 0;
}
```

---

### 4.4 **现代字符串：C++ string 类**

**C++ string 类**：`#include <string>` 自动管理内存，不怕越界拼接

- `+` 拼接
- `==`、`>`、`<` 比较
- `size()` / `length()` 返回长度（不含 `\0`）
- `substr(pos, len)` 切片
- `find(sub)` 查找子串，找不到返回 `string::npos`
- `cin >> s` 读字符串，遇空格停止
- `getline(cin, s)` 读整行（含空格）。

```C++
#include <string>
string s = "hello";
string t = s + ", world";
s == "hello"          // true
s < "world"           // true
t.size()              // 12
t[1]                  // 'e'
s.substr(1, 3)        // "ell"
t.find("world")       // 7
t.find("xyz") == string::npos //`find` 用 `npos` 判断，不是用 -1。
```

`cin >>` 和 `getline` 混用要先 `cin.ignore()` 清掉残留的换行：

```C++
cin >> age;
cin.ignore();        // 吃掉换行
getline(cin, name);
```

---

## 五、指针、引用与动态内存

---

### 5.1 **指针：存地址的变量**

* **定义指针**：`int x = 5; int* p = &x;`
    * `&` 表示**取地址**，拿到变量起始地址（十六进制显示）
    * 在定义里，`*` 表示 `p` 是一个指针。`int* p` 与 `int *p` 写法等价。
    * `int* p, q;`：`p` 是指针，`q` 不是指针！
* **解引用**：`*p`，访问 p 所指向地址的内存
    * `*p = 10` 是改值，`p = &y` 是改指向；
    * 对空指针/野指针解引用会崩溃。


```C++
int x = 5;
int* p = &x;
*p = 10;        // x 变成 10
cout << p;      // 打印地址（十六进制显示）
```

---

### 5.2 **数组与指针**

* 数组名 `a` 自动转换成首元素地址 `&a[0]`
* 令 `int* p = a`，则 `a[i] == *(a + i) == p[i] == *(p + i)`
* 指针加整数跳过 `i` 个元素（不是 `i` 字节）：`int* p = a; p + 1` 指向 `a[1]`
    * `p + 1` 对 `int*` 是 +4 字节、对 `char*` 是 +1 字节，按类型步进
* 指针是变量，可以自增自减（`p++`）
* 数组名 `a` 是常量指针，不能修改，比如不能自增自减（`a++`）

![数组与指针.png](images%2F%E6%95%B0%E7%BB%84%E4%B8%8E%E6%8C%87%E9%92%88.png)

---

### 5.3 **const 与指针**

- `const int* p`：**指向常量的指针**，`*p` 不能改，`p` 可以换
- `int* const p`：**指针常量**，`p` 不能换，`*p` 可以改

> `const int* p` 是“p 指向 const int”，`int* const p` 是“const 的 p 指向 int”；`const int* const p` 两个都不能改。

**指向指针的指针**：

![指向指针的指针.png](images%2F%E6%8C%87%E5%90%91%E6%8C%87%E9%92%88%E7%9A%84%E6%8C%87%E9%92%88.png)

---

### 5.4 **空指针 / 野指针 / 悬空指针 / 越界**

`int* p = nullptr`

- `nullptr` (C++11) 表示空指针，代替 `NULL`。

**三大坑**：

1. **野指针**：未初始化指针
2. **悬空指针**：指向已释放内存，如函数返回局部变量地址
3. **指针越界**：指向数组之外

![指针三大坑.png](images%2F%E6%8C%87%E9%92%88%E4%B8%89%E5%A4%A7%E5%9D%91.png)

**好习惯**：

- 指针定义时就初始化
- `delete` 后把指针置 `nullptr` 防止二次释放
- 解引用前先判断 `p != nullptr`

---

### 5.5 **引用：变量的别名**

`int& r = x;` 引用是变量的别名，不是新变量。

* **定义时必须初始化**
* 不能换绑（`r` 永远是 `x` 的别名）
* 不存在“空引用”
* 引用没有独立内存（同址）

```C++
int x = 5;
int& r = x;
r = 10;        // x 变成 10

void f(int& a) {
    // a 是外部传入变量的引用，修改 a 会直接修改外部变量
}
```

【易错提醒】`int& r;` 不初始化编译错；`r = y` 不是让 `r` 指向 `y`，而是把 `y` 的值赋给 `x`；引用不能返回局部变量的引用。

![引用vs指针.png](images%2F%E5%BC%95%E7%94%A8vs%E6%8C%87%E9%92%88.png)

---

### 5.6 **参数传递：值传递 / 指针传递 / 引用传递**

**引用最大的作用是做函数参数**。

三种参数传递方式：

1. **值传递**：形参是实参的拷贝，改形参不影响实参
2. **指针传递**：传地址拷贝，能通过指针改目标值
3. **引用传递**：直接操作实参本体
    - 想“只读不拷贝”用 `const` 引用（`const string&`）

![三种交换函数.png](images%2F%E4%B8%89%E7%A7%8D%E4%BA%A4%E6%8D%A2%E5%87%BD%E6%95%B0.png)

> 按值传大对象（`string/vector`）会整份拷贝，代价高
> 
> 函数内想改实参：指针或引用二选一，但指针可能为空，引用更安全

---

### 5.7 **内存布局：代码区 / 全局区 / 栈区 / 堆区**

程序运行时内存分四块：

* **代码区**：存放程序指令，只读
* **全局区**：存放全局变量、static 静态变量，**程序结束才释放**
* **栈区 stack**：存放局部变量、参数，**函数结束自动释放**，容量小
* **堆区**：`new`/`malloc` 申请，`delete` 手动释放，容量大，**生命周期由自己控制**

> 栈像自动收拾的临时工位，函数一结束东西自动清走；堆像自选仓库，你租多大就多大，但必须自己还钥匙（`delete`）。

![程序的内存布局.png](images%2F%E7%A8%8B%E5%BA%8F%E7%9A%84%E5%86%85%E5%AD%98%E5%B8%83%E5%B1%80.png)

注意：

* 栈区容量有限，递归太深或定义超大局部数组 → 栈溢出；
* 全局/静态变量不初始化 → 默认 0
* 局部变量不初始化 → 垃圾值。

---

### 5.8 **new / delete 动态内存**

- `new` **在堆上创建对象**并**返回指针**，`delete` 释放；
- `new[]` 分配数组必须配 `delete[]`

注意：

* 忘记 `delete` → **内存泄漏**
* 重复 `delete` 同一块内存 → 崩溃
* 内存不足 → `new` 失败，抛 `bad_alloc`。 

> `new` 和 `delete` 配对使用是内存管理的核心考点；后续会用智能指针替代裸 `new/delete`。智能指针会自动解决内存泄漏与重复释放问题

```C++
void func() {
    int* p = new int(5); 
    // 1. 变量 p 本身是个指针，它存在“栈（Stack）”上。
    // 2. 等号右边 new int(5) 在“堆（Heap）”上申请了 4 个字节，存入了 5。
    // 3. 然后，把堆上这块内存的地址，赋值给了栈上的 p。

    delete p;      // 释放的是堆上的那 4 个字节。此时 p 变成了“悬空指针”，但它自己依然在栈上。
    
    p = nullptr;   // 好习惯：释放后置空，防止产生“悬空指针”
    
    // 分配数组
    int* a = new int[10];
    delete[] a;
    a = nullptr;
}
```

---

## 六、函数

---

### 6.1 **函数声明与定义**

* **函数定义**：`返回类型 函数名(参数列表) { 函数体 }`。
* **函数原型声明**：`返回类型 函数名(参数类型);` 不带函数体
    * 调用前必须已有声明
* `main` 由系统调用，其他函数由代码调用。

> 声明和定义不一致、先调用后声明都会编译错；**函数不能嵌套定义**（C++ 不允许函数里再定义函数）。

![函数.png](images%2F%E5%87%BD%E6%95%B0.png)

![参数传递方式.png](images%2F%E5%8F%82%E6%95%B0%E4%BC%A0%E9%80%92%E6%96%B9%E5%BC%8F.png)

---

### 6.2 **默认参数**

`int f(int a, int b = 0, int c = 0);` 

* **默认参数必须从右往左连续设置**，不能跳着设（不能 a 默认而 b 不默认）
* 默认值通常在声明处给出 → 定义里可不再写
* 调用 `f(1)` 时 `b`、`c` 用默认值；`f(1, , 3)` 非法（不能跳过中间参数）
* 默认参数与重载混用 → 可能造成二义性

---

### 6.3 **函数重载**

**函数重载**：同名函数但**参数个数或类型不同**。编译器按实参匹配最合适的一个。

注意：

* 仅返回类型不同 → 不算重载
* 重载解析失败（都能转、都不精确） → 二义性。

```C++
int max(int a, int b);
double max(double a, double b); // 重载合法

int f();
void f(); // 重载非法，仅返回类型不同
```

![函数重载.png](images%2F%E5%87%BD%E6%95%B0%E9%87%8D%E8%BD%BD.png)

---

### 6.4 **内联函数 inline：编译期展开**

`inline int add(int a, int b) { return a + b; }` 编译时把函数体直接嵌入调用点，省去函数调用开销（压栈、跳转）。

- 适合小函数，大函数编译器可能忽略 `inline`。

**inline vs 宏**：

* 宏是“无脑替换文本”
* `inline` 是“安全地原地展开”，有类型检查、遵守作用域，是宏的现代替代

![内联函数.png](images%2F%E5%86%85%E8%81%94%E5%87%BD%E6%95%B0.png)

---

### 6.5 **递归：必须有终止条件**

**函数调用自己叫递归**。

注意：

* **递归必须有终止条件**
    * 每层调用都会在栈上压帧。
    * 忘记终止条件 → 栈溢出
* 深度太大的递归 → 可能超时/崩溃。可改用循环或记忆化

```C++
int fib(int n) { 
    if (n <= 1) return n; 
    return fib(n-1) + fib(n-2); 
}
```

---

### 6.6 **变量的存储类别：auto / static / extern**

- `auto`（默认）：局部变量，进入作用域创建（栈上），函数结束销毁
- `static` 局部变量：只初始化一次，函数多次调用间保留值
- `static` 全局变量：限定本文件可见
- `extern`：声明别的文件里定义的全局变量
- `register`（现代 C++ 已废弃）：建议放寄存器

`void counter() { static int n = 0; ++n; cout << n; } // 每次调用输出 1、2、3......`

注意：

- `static` 局部变量初始化只执行一次
- 全局变量与局部变量同名时，局部屏蔽全局，**用 `::x` 访问全局**
- 局部变量不初始化是垃圾值

---

## 七、结构体、枚举与预处理指令

---

### 7.1 **结构体与内存对齐**

结构体把不同类型打包成一个整体。

- 访问成员用 `.`，指针访问用 `->`。

```C++
struct Student {
    string name;
    int age;
    double score;
};

Student s; // C++ 可省略 struct
s.name = "Tom";
s.age = 18;

Student* p = &s;
p->age = 19;    // p->age 等价于 (*p).age
```

`sizeof(结构体)` 不等于各成员大小之和（有对齐填充）

- **内存对齐**：成员按自身大小对齐存放，结构体总大小是最大对齐单位的整数倍，中间可能有填充字节，成员声明顺序会影响填充大小（比如前两个 `char` 存在一个 `int` 大小的空间）。

---

### 7.2 **枚举：enum 与 enum class**

枚举定义一组有名字的整数常量。例如 `enum Color { RED, GREEN, BLUE };`

- 默认从 0 递增，也可指定值，后面接着递增。

* 旧 `enum` 的枚举值会泄漏到外层作用域且能隐式转 `int`（易冲突）
* 新代码（C++11）优先 `enum class`。
    * `enum class` 是强类型枚举：不隐式转换为 `int`，必须用 `类型::值` 访问，避免名字冲突。

```C++
enum class Color { RED, GREEN };
Color c = Color::RED;
int n = static_cast<int>(c); // 强类型需显式转换
```

![枚举.png](images%2F%E6%9E%9A%E4%B8%BE.png)

---

### 7.3 **类型别名：typedef / using**

- `typedef unsigned long ul;` 给类型 `unsigned long` 起别名 `ul`
- C++11 推荐 `using ul = unsigned long;` 写法更清晰，且能配合模板

---

### 7.4 **宏与预处理指令：#define / #include / 条件编译**

以 `#` 开头的指令是**预处理指令**，**在编译之前执行**。

1. `#define` 定义宏（文本替换）
    - `#define PI 3.14159` 把 `PI` 替换为 `3.14159`
    - 文本替换的陷阱：**带参数的宏**必须整体加括号：`#define SQ(x) ((x)*(x))`。否则当 `SQ(1+2)` 会展开成 `1+2*1+2`
    - 宏无作用域无类型，能用 `const/constexpr` 就别用宏。
2. `#include` 把另一个文件的内容原样复制进来
    - `#include <iostream>` 去系统目录找头文件
    - `#include "my.h"` 先在当前目录找，找不到再到系统目录找。
3. `#ifdef` / `#ifndef` / `#endif` **条件编译**，根据**特定的宏是否被定义**决定某部分代码是否参与编译
    - 常用来写调试代码和跨平台代码。

**条件编译的应用场景**：

- 场景一：防止头文件重复包含（最常见）

```C++
// math_utils.h
#ifndef MATH_UTILS_H  // 如果没有定义过这个宏
#define MATH_UTILS_H  // 那么现在就定义它

// 这里放你的声明和定义
int add(int a, int b);

#endif // MATH_UTILS_H 结束
```

- 场景二：调试代码的开关

```C++
#define DEBUG // 开启调试模式（如果在代码里注释掉这一行，下面的 cout 就不会被编译）

#ifdef DEBUG
    cout << "调试信息";
#endif

#undef DEBUG  // 用完就取消，防止污染后面代码

#ifdef NDEBUG // No DEBUG 的缩写
    cout << "正式版（性能最优）";
#else
    cout << "测试版（包含调试信息）";
#endif
```

- 场景三：跨平台兼容代码

```C++
#ifdef _WIN32
    // Windows 专属代码
    #include <windows.h>
    Sleep(1000);
#elif __linux__
    // Linux 专属代码
    #include <unistd.h>
    sleep(1);
#else
    // 其他平台
    #error "不支持的操作系统"
#endif
```

宏从哪来？—— **编译器的“命令行参数”**： 
现代开发（比如使用 CMake、Makefile 或直接在 VS/VS Code 里配置）时，我们通常在编译选项中定义宏，而不是在代码里写。例如，使用 GCC/G++ 编译时：

```bash
# 编译测试版（包含调试信息）
g++ main.cpp -DDEBUG -o app_debug

# 编译发布版（定义 NDEBUG，这是 C++ 标准推荐的 Release 宏）
g++ main.cpp -DNDEBUG -o app_release
```

---

### 7.5 **多文件程序与头文件保护**

大程序要拆成多个文件，头文件 `.h` 放声明，源文件 `.cpp` 放定义。用到的地方 `#include` 头文件。

头文件保护（避免重复包含）：

- `#ifndef MY_H #define MY_H ... #endif` 守卫
- `#pragma once` 一行搞定（现代编译器普遍支持，更简洁）。

![多文件程序与头文件保护.png](images%2F%E5%A4%9A%E6%96%87%E4%BB%B6%E7%A8%8B%E5%BA%8F%E4%B8%8E%E5%A4%B4%E6%96%87%E4%BB%B6%E4%BF%9D%E6%8A%A4.png)

---

## 八、类与对象

### 8.1 **面向对象三大特征：封装 / 继承 / 多态**

**面向对象思想**

![面向对象.png](images%2F%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1.png)

**面向对象三大特征**：

* **封装**：把数据和操作数据的函数绑在一起，对外只暴露接口
* **继承**：子类复用父类，is-a 关系
* **多态**：同一接口不同实现（虚函数）。

**对象是类的实例**

![类与对象.png](images%2F%E7%B1%BB%E4%B8%8E%E5%AF%B9%E8%B1%A1.png)

---

### 8.2 **封装：访问控制**

`class` 与 `struct` 唯一区别：默认访问权限

- `class` 默认 `private`
- `struct` 默认 `public`。

**访问控制**：

- `public` 任何地方可访问
- `private`（默认）仅本类成员函数可访问（这是封装的关键）
- `protected` 本类及派生类可访问

![封装.png](images%2F%E5%B0%81%E8%A3%85.png)

```C++
class Student {
private:
    string name;
public:
    void setName(string n) {
        name = n;
    }
};
```

在类外定义成员函数要写 `类名::`，例如 `Student::setName`。

![在类外定义成员函数.png](images%2F%E5%9C%A8%E7%B1%BB%E5%A4%96%E5%AE%9A%E4%B9%89%E6%88%90%E5%91%98%E5%87%BD%E6%95%B0.png)

---

### 8.3 **this 指针**

`this` 是**指向“当前调用成员函数的那个对象”的指针**。成员函数访问的成员变量都是通过 `this` 找到的。

- `this` 指针作用：同一份函数代码操作不同对象的数据
- `this` 不能改指向（`const` 指针）
- 静态成员函数没有 `this`。

```C++
void set(int age) { 
    this->age = age; 
} // 参数与成员同名时用 this 区分
```

返回 `*this` 还可实现链式调用：

```C++
Counter &add(int n) {
    this->n += n; return *this; 
}
c.add(1).add(2).add(3); // 链式
```

---

### 8.4 **对象里只存数据成员**

**对象里只存成员变量**。

- `sizeof(空类) = 1`：编译器给空对象占 1 字节，保证不同对象的地址不同。
- 成员函数不占用对象空间（在代码区），所有对象共用一份
- 只有数据成员决定对象大小（含虚函数时会多个 vptr）

> 有虚函数的类会额外多一个虚表指针（8 字节）。

## 九、构造函数与析构函数

### 9.1 **构造函数**

构造函数 `类名(){}` 在创建对象时自动调用：

* 函数名为类名 
* 无返回类型
* 可重载，可以有默认参数
* 一般是 `public`
* 构造函数不能调虚函数（多态未建立）

**默认构造函数**：

* 没定义任何构造函数 → 编译器生成一个什么都不做的默认构造函数
* 定义了构造函数 → 默认构造就不再自动生成
* 定义了带参构造 → `Student s;` 不写任何参数会编译错（默认构造没了）→ **必须手动写默认构造函数**

```C++
#include <iostream>
#include <string>
using namespace std;

class Student {
private:
    string name;
    int age;

public:
    // 1. 默认构造函数（无参）
    // 因为自己写了带参构造，编译器不再自动生成，所以必须手动写出来
    Student() {
        name = "unknown";
        age = 0;
        cout << "默认构造函数被调用" << endl;
    }

    // 2. 带参数的构造函数（使用初始化列表）
    Student(string n, int a) : name(n), age(a) {
        cout << "带参构造函数被调用" << endl;
    }

    // 3. 带默认参数的构造函数（注意：如果同时存在 1 和 3，可能会产生二义性，这里仅作演示）
    // Student(string n = "Tom", int a = 18) : name(n), age(a) {}

    void print() {
        cout << "姓名: " << name << ", 年龄: " << age << endl;
    }
};
```

---

### 9.2 **初始化列表**

**初始化列表**：`Student(string n, int a) : name(n), age(a) {}`。

**必须用初始化列表的三种成员**：

1. `const` 成员
2. 引用成员
3. 没有默认构造函数的成员对象

![初始化列表.png](images%2F%E5%88%9D%E5%A7%8B%E5%8C%96%E5%88%97%E8%A1%A8.png)

```C++
class A { 
    const int c; 
    int& r; 
public: 
    A(int x, int& y) : c(x), r(y) {} 
}; // 必须初始化列表
```

易错点：实际初始化顺序与初始化列表的书写顺序无关，只与成员声明顺序有关——声明在前先初始化!

---

### 9.3 **析构函数**

析构函数 `~类名(){}` 负责释放资源：

* 无参数、无返回类型
* 不能重载，一个类只有一个
* 对象销毁时自动调用：
    * 局部对象（栈对象）离开作用域时
    * `new` 出来的堆对象 `delete` 时
    * 全局对象 `main` 结束后

![析构函数.png](images%2F%E6%9E%90%E6%9E%84%E5%87%BD%E6%95%B0.png)

```C++
class Buf {
    int *data;
public:
    Buf() { data = new int[100]; }
    ~Buf() { delete[] data; }  // 收尾
};
```

> 需要释放资源的类必须自己写析构；局部对象一定会被析构（哪怕函数提前 return 或抛异常）。

---

### 9.4 **构造与析构顺序：先构造的后析构**

* 同一作用域的**局部对象先构造的后析构**
* **全局对象**在 `main` 之前构造、`main` 之后析构
* **静态局部对象**第一次执行到才构造、程序结束时析构
* 对象数组逐个成员按顺序构造

![构造与析构顺序.png](images%2F%E6%9E%84%E9%80%A0%E4%B8%8E%E6%9E%90%E6%9E%84%E9%A1%BA%E5%BA%8F.png)

![构造与析构顺序案例.png](images%2F%E6%9E%84%E9%80%A0%E4%B8%8E%E6%9E%90%E6%9E%84%E9%A1%BA%E5%BA%8F%E6%A1%88%E4%BE%8B.png)

![对象数组与new.png](images%2F%E5%AF%B9%E8%B1%A1%E6%95%B0%E7%BB%84%E4%B8%8Enew.png)

---

## 十、拷贝构造

---

### 10.1 **拷贝构造函数**

**用已有对象初始化新对象**时调用拷贝构造。

- 形参必须是引用（通常 `const` 引用）：`Point(const Point &p)`
    - 如果参数按值传 `Point(Point p)` → 会无限递归（拷贝构造又要拷贝构造）
  
- 什么时候调用拷贝构造：

1. 用对象初始化对象 `A b = a;`
    - `A b = a;` 与 `b = a;` 不同，前者是拷贝构造，后者是赋值。
2. 函数按值传参 `Point(Point p)`
3. 函数按值返回临时对象 `Point g(){return p;}`
    - C++17 返回值优化：返回临时对象时强制省略拷贝构造，**直接在调用处构造对象**

![什么时候调用拷贝构造.png](images%2F%E4%BB%80%E4%B9%88%E6%97%B6%E5%80%99%E8%B0%83%E7%94%A8%E6%8B%B7%E8%B4%9D%E6%9E%84%E9%80%A0.png)

---

### 10.2 **浅拷贝 / 深拷贝 / 拷贝赋值运算符**

**浅拷贝（默认）**：逐成员拷贝

- `int`、`double` 类型的成员变量没问题
- 指针成员 → 默认拷贝是浅拷贝 → 只拷贝地址 → 两个对象指向同一块堆内存 → **析构时双重释放** → 崩溃

> 指针成员必须深拷贝!

![浅拷贝.png](images%2F%E6%B5%85%E6%8B%B7%E8%B4%9D.png)

**深拷贝**：拷贝构造里 `new` 一块新内存并复制内容，让两个对象各自持有独立资源。

**拷贝赋值运算符** `A& operator=(const A& o) { 拷贝赋值 }`。标准写法：

1. 自赋值检查 `if (this == &o) return *this;`
2. 释放旧资源
3. 分配新内存并拷贝新资源
4. 返回 `*this` → 支持连等 `a = b = c`

![深拷贝.png](images%2F%E6%B7%B1%E6%8B%B7%E8%B4%9D.png)


### 10.3 **三法则 / 五法则 / 零法则**

* **三法则**：若类需要自定义**析构函数**、**拷贝构造函数**、**拷贝赋值运算符**中的任何一个，那多半三个都需要（否则资源管理不一致）
* **五法则**：C++11 在三法则的基础上加**移动构造**、**移动赋值**
* **零法则**：现代 C++ 用 `string`/`vector`/智能指针 管理资源，就不用写任何特殊成员。

> 现代代码优先零法则，能少写就少写。

---


## 十一、静态成员、常量与友元

---

### 11.1 **静态数据成员**

`static` 数据成员属于类本身，所有对象共享一份，不占对象空间。

* 在类外定义/初始化一次（如 `int Student::count = 0;`），因为它是全局生命周期。
* C++17 起也可以在类内写 `inline static int count = 0;` 直接初始化
* 可用类名访问：`Student::count`

---

### 11.2 **静态成员函数**

`static` 成员函数没有 `this`，因此只能访问静态成员，不能访问非静态成员

* 可用类名直接调用，不依赖对象。例如 `Student::getCount()`

静态成员典型应用：**对象计数器**：

```C++
class Student {
private:
    static int count;
public:
    Student() { count++; }
    ~Student() { count--; }
    static int getCount() { return count; }
};
int Student::count = 0; // 类外定义
// sizeof(Student) 不含 count，因为 count 是静态成员，存放在全局区
```

---

### 11.3 **常对象与常成员函数**

**常对象**的数据成员不能修改。如 `const A a;`

- 常对象只能调用常成员函数。`const` 对象调非 `const` 函数 → 编译错

**常成员函数**在参数表后加 `const` 声明“这个函数不修改对象”，如 `int getAge() const { return age; }`

- 常成员函数里修改成员变量 → 编译错
- 建议：所有不修改数据的成员函数都声明成 `const`，否则常对象调用会编译错
- 想修改个别成员用 `mutable`
- `const` 重载（同一函数名 `const` 与非 `const` 版本）是合法的，按对象是否 `const` 选版本

```C++
class A {
    int age;
    mutable int n;
public:
    int getAge() const { return age; }   // 常成员函数，不能改 age
    void setAge(int a) { age = a; }      // 普通成员函数
    void test() const { n++; }           // 想改成员需 mutable

    void print() const { cout << "const"; } // const 重载
    void print() { cout << "non-const"; }
};

const A a;
// a.setAge(10);  // 错误：常对象不能调非 const 函数
a.getAge();       // 正确
a.print();        // 调用 const 版本的 print
```

---

### 11.4 **友元**

`friend` 声明让**外部函数/类访问本类私有成员**。

**友元关系性质**：单向、不传递、不继承

![友元.png](images%2F%E5%8F%8B%E5%85%83.png)

友元典型用途：重载 `<<` 运算符

```C++
class A {
    int x;
    friend ostream& operator<<(ostream&, const A&);
};
```

> 友元破坏封装，只在必要时用（如运算符重载）

---

### 11.5 **组合与对象成员**

一个类的对象作为另一个类的数据成员叫**成员对象**，这种关系叫**组合**。例如 `class B { A a; };`

- **成员对象先构造** （按声明顺序），再构造自己
- **析构相反**，先析构自己再析构成员

> 优先用组合而非继承（面向对象设计原则）

has-a 是组合与聚合的统称，组合是强 has-a，聚合是弱 has-a

---

## 十二、运算符重载

---

### 12.1 **重载形式与规则**

重载运算符让自定义类型支持 `+ - * / == <<` 等。例如 `A operator+(const A& other) const`

![运算符重载.png](images%2F%E8%BF%90%E7%AE%97%E7%AC%A6%E9%87%8D%E8%BD%BD.png)

**运算符重载的两种形式**：

* 成员函数：隐含传入 `this` 指针作为左操作数
* 友元 + 全局函数：所有操作数都要作为参数传进来
    * 适用场景：左操作数不是本类对象。例如 `<<` / `>>` 必须重载为全局函数（友元），因为左操作数是 `ostream/istream` 对象而不是 `this` 指针

**重载规则**：

* 至少一个操作数是自定义类型
* 不能改变优先级、结合性、操作数个数
* 不能发明新运算符

> 五个不能重载的运算符：`.` 成员访问、`::` 作用域、`?:` 三目、`.*` 成员指针、`sizeof`
> 四个重载为成员函数的运算符：`=`、`[]`、`()`、`->`


```C++
// 算术运算符（加、减）
// 成员函数实现：a + b 等价于 a.operator+(b)
Complex operator+(const Complex& c) const { 
    return Complex(real + c.real, imag + c.imag); 
}

// 复合赋值运算符（+=、-=）
// 通常返回自身的引用（*this），因为要支持连续赋值（如 a += b += c）
Complex& operator+=(const Complex& c) { 
    real += c.real; 
    imag += c.imag;
    return *this; 
}
```

---

### 12.2 **输出运算符 << 重载**

输入 / 输出运算符 `>>` / `<<` 重载必须采用 **友元 + 全局函数** 的形式

* 写成全局函数并在类里声明 `friend`，因为左操作数 `ostream` 不是你的类。
* 返回 `ostream` 引用支持连写 `cout << a << b`。
* 输入 `>>` 与 `<<` 重载的**区别**：第二个参数绝对不能加 `const`

```C++
class A { 
    int x; 
    // 1. 类内声明友元（输出和输入）
    friend ostream& operator<<(ostream&, const A&); 
    friend istream& operator>>(istream&, A&);
};

// 2. 类外实现输出
ostream& operator<<(ostream& os, const A& a) { 
    return os << a.x; 
}

// 3. 类外实现输入
istream& operator>>(istream& is, A& a) { 
    return is >> a.x; 
}
```


---

### 12.3 **自增运算符 ++ 重载**

- 前置 `++`：`A& operator++() { ++n; return *this; }`
- 后置 `++` 多一个占位 `int` 参数：`A operator++(int) { A old = *this; ++n; return old; }`

> 前置 `++` 返回自身引用
> 
> 后置 `++` 返回旧值的副本（返回对象不是引用），后缀的 `int` 参数只是占位符

```C++
#include <iostream>
using namespace std;

class Counter {
private:
    int n; // 成员变量

public:
    // 构造函数
    Counter(int val) : n(val) {}

    // 1. 前置 ++：先加，再返回自身引用
    Counter& operator++() {
        ++n;
        return *this;
    }

    // 2. 后置 ++：先保存旧值，再加，返回旧值（必须按值返回，不能返回引用）
    Counter operator++(int) {
        Counter old = *this; // 拷贝当前状态
        ++n;                 // 自身加 1
        return old;          // 返回旧状态的副本
    }

    // 辅助打印函数
    void print() const {
        cout << "n = " << n << endl;
    }
};

int main() {
    Counter c(5);
    
    // 验证后置 ++
    Counter a = c++;  
    cout << "执行 c++ 后：" << endl;
    cout << "a: "; a.print(); // a=5
    cout << "c: "; c.print(); // c=6

    // 验证前置 ++
    Counter b = ++c;  
    cout << "执行 ++c 后：" << endl;
    cout << "b: "; b.print(); // b=7
    cout << "c: "; c.print(); // c=7

    return 0;
}
```

---

### 12.3 **赋值运算符 = / 下标运算符 [] / 调用运算符 () 重载**

* 赋值运算符 `operator=`：让对象可以赋值给其他对象
* 下标运算符 `operator[]`：让对象可以像数组一样用下标访问元素
* 调用运算符 `operator()`：让对象可以像函数一样被调用。这种对象也叫**函数对象（仿函数）**

```C++
Arr &operator=(const Arr &o) { ...; return *this; }
a = b = c;

int &operator[](int i) { return d[i]; }
a[1] = 5;

int operator()(int a, int b) const { return a + b; }
add(2, 3)  // 5
```

---

## 十三、继承

---

## 十四、虚函数与多态

---

## 十五、模板

---

## 十六、STL 容器与算法

---

## 十七、输入输出流

---

## 十八、文件操作

---

## 十九、字符串流

---

## 二十、异常处理

---

## 二十一、智能指针

---

## 二十二、移动语义

---