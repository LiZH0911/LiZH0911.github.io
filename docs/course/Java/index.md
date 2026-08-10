# Java 基础

该笔记参考的课程链接：

- [黑马程序员 AI+Java](https://www.bilibili.com/video/BV1TJxCzSEEZ?spm_id_from=333.788.videopod.episodes&vd_source=46f99c7c1ed609a31f70615a4551767f&p=2)

## 一、Java 入门

## 二、Java 语法

### 2.1 **字面量**

**定义**：字面量是程序中直接书写的固定值（数据）。

**字面量的种类**：

- 整数：`123`
- 浮点数：`3.14`
- 字符串（加双引号）：`"Hello, World!"`
- 字符（加单引号）：`'H', '0', '男'`
- 布尔值：`true/false`
- 空值：`null`

### 2.2 **变量**

**定义**：变量是程序中存储数据的容器（经常会发生改变的数据）。

```Java
数据类型 变量名 = 数据值;
int a = 10;
```

**数据类型**：为空间中存储的数据加入类型限制

**用途**：输出打印、参与计算、记录数据

**注意事项**：

1. 一个变量只能存储一个值
2. 变量定义的时候必须赋值才可以使用
3. 一条语句可以定义多个变量，也可以连续赋值

### 2.3 **计算机的存储规则**

- 在计算机中，任意数据都是以二进制的形式来存储的
- 字节是计算机最小的存储单元，1个字节 = 8个 bit 位

### 2.4 **数据类型**

**分类**：基本数据类型，引用数据类型

**基本数据类型**：

- 整数类型：byte, short, int, long ——> 占用内存（字节）：1，2，4，8
- 浮点类型：float, double ——> 占用内存（字节）：4，8
- 字符类型：char ——> 占用内存（字节）：2
- 布尔类型：boolean ——> 占用内存（字节）：1

### 2.5 **标识符**

**定义**：标识符是程序员在代码中为变量、函数、类等元素所起的名字。

**标识符的命名规则（规定）**：

1. 只能包含字母(`a-z,A-Z`)、数字(`0-9`)、下划线(`_`)、美元符号(`$`)
2. 不能以数字开头
3. 不能是关键字，如 `class`, `public`, `static` 等
4. 区分大小写

**标识符的命名规范**：见名知意，驼峰命名

- 小驼峰命名法（针对变量、方法）：每个单词的首字母小写，不使用下划线或美元符号，如 `userName`
- 大驼峰命名法（针对类名）：每个单词的首字母大写，不使用下划线或美元符号，如 `SnakeGame`

### 2.6 **键盘录入**

**定义**：键盘录入是程序从键盘获取数据的过程。

**实现**：使用 `Scanner` 类来实现键盘录入

```Java
import java.util.Scanner;
public class KeyboardInput {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.println("请输入一个整数：");
        int data = scanner.nextInt();
        System.out.println("输出整数：" + data);
        
        System.out.println("请输入一个小数：");
        double data2 = scanner.nextDouble();
        System.out.println("输出小数：" + data2);
        
        System.out.println("请输入字符串：");
        String data3 = scanner.next();
        System.out.println("输出字符串：" + data3);
    }
}
```