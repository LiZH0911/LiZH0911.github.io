# Java 基础

该笔记参考的课程链接：

- [黑马程序员 AI+Java](https://www.bilibili.com/video/BV1TJxCzSEEZ?spm_id_from=333.788.videopod.episodes&vd_source=46f99c7c1ed609a31f70615a4551767f&p=2)

## 一、Java 入门

## 二、数据存储与运算

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

**用途**：输出打印、参与计算、记录数据

**注意事项**：

1. 一个变量只能存储一个值
2. 变量定义的时候必须赋值才可以使用
3. 一条语句可以定义多个变量，也可以连续赋值

### 2.3 **计算机的存储规则**

- 在计算机中，任意数据都是以二进制的形式来存储的
- 字节是计算机最小的存储单元，1个字节 = 8个 bit 位

### 2.4 **数据类型**

**数据类型**：为空间中存储的数据加入类型限制。

- 基本数据类型
- 引用数据类型

**基本数据类型**：

- 整数类型：byte, short, int, long ——> 内存（字节）：1，2，4，8
- 浮点类型：float, double ——> 内存（字节）：4，8
- 字符类型：char ——> 内存（字节）：2
- 布尔类型：boolean ——> 内存（字节）：1

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

### 2.7 **运算符**

**算术运算符**：`+, -, *, /, %`

**算术运算符-①数字运算**：

> 小数计算有可能不精确

**案例-数值拆分**：给定非负整数 number，提取其个位、十位、百位

```Java
int number = 123;
// 个位
int ge = number % 10;
System.out.println("个位：" + ge);
// 十位
int shi = number / 10 % 10;
System.out.println("十位：" + shi);
// 百位
int bai = number / 100 % 10;
System.out.println("百位：" + bai);
```

**案例-时间转换**：给定秒数 seconds，将其转换为对应的小时数、分钟数和秒数，使得总时间不变

```Java
int seconds = 3661
// 秒数
int second = seconds % 60;
System.out.println("秒数：" + second);
// 分钟数
int minute = seconds / 60 % 60;
System.out.println("分钟数：" + minute);
// 小时数
int hour = seconds / 3600;
System.out.println("小时数：" + hour);
```

**类型转换**：在数字运算中，类型不一样的不能运算，需要转成同类型。Java 的类型转换包括隐式转换和强制转换。

**类型转换-①隐式转换**：把取值范围小的数据转换为取值范围大的数据，例如：`int` 转换为 `double`

- 触发时机：不同类型的数据进行计算，默认采取隐式转换，Java自动转换，无需我们写代码
- 转换步骤1：如有byte short类型的数据，先转换为int
- 转换步骤2：把取值范围小的提升为取值范围大的，再进行计算

**类型转换-②强制转换**：把取值范围大的数据转换为取值范围小的数据（去掉不要的），例如：`double` 转换为 `int`

- 触发时机：不会自动触发，需要手动书写代码
- 书写格式：`目标数据类型变量名 = (目标数据类型)被强转的数据;`

**算术运算符-②字符运算**：本质是字符的ASCII码值运算。

```Java
// 实现字母的大小写转换，将大写字母转换为小写字母
char ch = 'A';
char ch2 = (char)(ch + 32); // 'A' 的 ASCII 码值是 65，'a' 的 ASCII 码值是 97，相差 32
System.out.println("转换后的字符：" + ch2);
```

**算术运算符-③字符串运算**：

- 字符串只有`+`运算
- `任意数据 + 字符串`都是拼接操作，并产生一个新的字符串

**自增自减运算符**：`++, --`

**赋值运算符**：`=, +=, -=, *=, /=, %=`

**比较运算符**：`==, !=, >, <, >=, <=`

**逻辑运算符**：`&, |, ！`

**短路逻辑运算符**：`&&, ||`

- && 左边为 false，右边不执行
- || 左边为 true，右边不执行
- 和单个`&, |`是一样的，只不过提高了效率

**三元运算符**：

- 格式：`关系表达式 ? 表达式1 : 表达式2`
- 先计算关系表达式，若为 true，则返回表达式1，反之返回表达式2

**运算符的优先级**：小括号优先于其他

## 三、流程控制语句

### 3.1 **if 条件判断**

```Java
if (表达式1){
    执行操作1
}else if (表达式2){
    执行操作2
}else {
    执行操作3
}
```

### 3.2 **switch 条件判断**

```Java
switch (表达式) {
    case 值1:
        执行操作1
        break;
    case 值2:
        执行操作2
        break;
    default:
        执行操作3
}
```

### 3.3 **for 循环**

```Java
for (初始化; 条件; 步进) {
    执行操作
}
```

### 3.4 **while 循环**

```Java
while (条件) {
    执行操作
}
```

**break 关键字**：不能单独书写，必须在 switch 或 循环中，表示结束、跳出整个循环

**continue 关键字**：不能单独书写，必须在循环中，表示跳过本次循环，继续下一次循环

## 四、数组

### 4.1 **数组的静态初始化**

**数组的定义**：数组是存储相同类型数据的容器。

**静态**：在定义变量、数组、对象的时候，数据是静止、确定的。

**初始化**：定义 + 赋值同时进行

```Java
// 完整格式
数据类型 数组名[] = new 数据类型[]{数据值1, 数据值2, 数据值3, ...};
数据类型[] 数组名 = new 数据类型[]{数据值1, 数据值2, 数据值3, ...};
// 简写形式
数据类型 数组名[] = {数据值1, 数据值2, 数据值3, ...};
数据类型[] 数组名 = {数据值1, 数据值2, 数据值3, ...};

int array[] = {1, 2, 3, 4, 5};
```

**数组的特点**：

1. 连续的空间
2. 一旦定义，长度不可变

### 4.2 **数组中元素的访问**

**索引**：索引就是数组的一个编号，也叫作:角标、下标、编号。从0开始，连续+1，不间断。

**获取元素**：

```Java
变量 = 数组名[索引]
int num = arr[5]
```

**修改元素**：

```Java
数组名[索引] = 数据值
arr[5] = 10
```

### 4.3 **数组的遍历**

```Java
for (int i = 0; i < array.length; i++) {
    System.out.println(array[i]);
}
```

### 4.4 **数组的动态初始化**

**动态**：在定义变量、数组、对象的时候，数据是不确定的。

```Java
// 完整格式
数据类型 数组名[] = new 数据类型[长度];
数据类型[] 数组名 = new 数据类型[长度];

int arr[] = new int[5];
```

> 数组的常见问题：索引越界。索引范围是 0 ~ 长度-1

## 五、方法

### 5.1 **方法的定义和调用**

**定义**：方法是一段可以重复使用的代码块，用于实现特定功能。

```Java
public class Method {
    // 主程序
    public static void main(String[] args) {
        int sum = getSum(5, 3);
        System.out.println("和为：" + sum);
    }
    
    /*
    方法定义的格式：
    public static 返回值类型 方法名(参数列表) { 
        方法体 
        return 返回值;
    }
    */
    
    // 定义一个方法，求两个整数的和
    public static int getSum(int a, int b) {
        return a + b;
    }
}
```

### 5.2 **方法的重载**

**定义**：方法重载是方法名相同，参数列表不同（参数类型不同，参数个数不同，参数顺序不同），返回值类型可以相同，也可以不同。

## 六、原理篇

### 6.1 **Java 的运行机制**

**Java程序运行的过程**：.java 文件 --> .class 字节码文件 --> 运行结果

**Java运行环境**：虚拟机，利用虚拟机可以实现跨平台

### 6.2 **内存和内存地址**

**内存**：内存是软件运行时用来临时存储数据的区域。

**内存地址**：内存中每个小格子（1个字节大小的存储单元）的编号

- 32 位的操作系统，有 2^32 个内存地址。即 4GB 的内存空间
- 64 位的操作系统，有 2^64 个内存地址。即 16EB 的内存空间

> 通常将二进制的内存地址转换为十六进制方便阅读

### 6.3 **java 的内存分配**

**内存分配**：程序运行时，数据存储在内存中，内存被划分为多个区域，每个区域有不同的用途。

- 栈内存：<span style="color: red;">方法</span>，用于存储方法调用和局部变量。
- 方法区：<span style="color: red;">.class 字节码信息</span>。用于存储类的结构信息。
- 堆内存：<span style="color: red;">new关键字</span>。用于存储对象实例。
- 本地方法栈：调用本地 Native 方法
- 程序计数器：每个线程独立、记录当前线程执行的字节码指令地址（行号）

> 一般都会用到栈内存和方法区，有 new 关键字还会用到堆内存

### 6.4 **数组的内存分配**

**基本数据类型**：存储<span style="color: red;">真实数据</span>，在栈内存中。

**引用数据类型**：存储<span style="color: red;">内存地址</span>，而真实数据存储在指定内存地址的堆内存中。

> 数组是一种引用数据类型

```Java
public class Memory{
    public static void main(String[] args) {
        int[] arr = {1, 2, 3}};
    }
}
```

> 以上代码用到的内存：栈内存（`int[] arr`）、方法区、堆内存（`{1, 2, 3}`）
> 
> `arr` 存储内存地址，通过 `arr[索引]` 可以访问数组的内容

### 6.5 **数组在方法中传递**

```Java
public class Memory{
    public static void main(String[] args) {
        int[] arr = {1, 2, 3}};
        change(arr);
    }
    
    // 数组作为方法的参数，传递的是内存地址！可以通过内存地址修改堆区的数组内容！
    public static void change(int[] arr) {
        temp = arr[0];
        arr[2] = arr[2];
        arr[0] = temp;
    }
}
```

## 七、面向对象

### 7.1 **概述**

**面向过程编程的思想**：把一个需求分解成一系列要执行的步骤，然后按照步骤依次执行这些任务（关注的是流程、步骤）。

**面向对象编程的思想**：把一个人/物的特征和功能打包到一起，是面向对象编程的基本单元（关注的是谁来帮我做这件事儿）。

### 7.2 **类与对象**

**类（class）**：描述的是一组具有相同属性（特征）和方法（功能/行为）的模板。

**对象（object）**：对象是类的实例，是基于类创建出来的（实例对象）。

**类的定义和对象的创建**：

```Java
public class Dog{
    // 属性
    String name;
    int age;
    
    // 方法/行为
    public void eat() {
        System.out.println("吃吃吃");
    }
    
    public void sleep() {
        System.out.println("睡睡睡");
    }
}

public class Test{
    public static void main(String[] args) {
        // 创建对象
        Dog dog = new Dog();
        // 调用对象的属性
        dog.name = "旺财";
        dog.age = 2;
        // 调用对象的方法
        dog.eat();
        dog.sleep();
    }
}
```

> 描述一类事物的类叫做 Javabean 类，Javabean 类可以写属性和行为
> 
> 带有 main 方法的类叫测试类

### 7.3 **面向对象中的数据安全问题**

**数据安全问题**：在面向对象编程中，数据安全问题是指如何保护对象的属性不被外部直接访问和修改。

**private 关键字**：private 关键字是一个权限修饰符，可以修饰成员变量和成员方法，表示私有，即只能在当前类中访问。

**get/set 方法**：get/set 方法用于获取/设置私有属性的值，用 public 修饰。

### 7.4 **就近原则和 this 关键字**

**成员变量与局部变量**：成员变量定义在方法外部，局部变量定义在方法内部。

**就近原则**：在方法中，如果局部变量和成员变量同名，局部变量就近使用，即先使用局部变量，再使用成员变量。

**this 关键字**：this 关键字表示当前对象，即当前正在执行的方法所属的对象。this 关键字可以用于访问成员变量、成员方法和构造方法。

```Java
System.out.println(name); // 就近原则
System.out.println(this.name); //使用成员变量
```

### 7.5 **构造方法**

**构造方法**：构造方法用于创建对象时初始化对象的属性。构造方法的名称必须与类名相同，且没有返回类型（包括 void）。

```Java
public class Student{
    private String name;
    private int age;
    // 空参构造方法
    public Student() {
        System.out.println("构造方法");
    }
    
    // 带全部参数的构造方法
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
        System.out.println("构造方法");
    }
}
```

> 习惯：无论是否使用，都手动书写空参构造方法和带全部参数的构造方法

## 八、面向对象原理篇

### 8.1 **对象的内存分配**

**对象的内存分配**：对象在堆内存中分配，对象的属性在堆内存中分配，对象的方法在方法区中分配。

1. 加载 class 字节码文件
2. 申明等号左边的局部变量
3. 堆中开辟空间：堆内存中存储<span style="color: red;">成员变量</span>和<span style="color: red;">成员方法的地址</span>（该地址指向方法区）
4. 默认初始化：给对象的属性赋默认值
5. 显式初始化：若成员变量在定义时有赋初始值，则执行赋值操作（一般不赋初值，可忽略）
6. 构造方法初始化
7. 赋值地址值：将堆内存中的对象地址值赋给栈内存中的局部变量

### 8.2 **在方法中传递对象**

**在方法中传递对象的本质**：传递的是内存地址，与数组类似

### 8.3 **this 关键字**

**this关键字的本质**：所在方法调用者的地址值

## 九、面向对象进阶

### 9.1 **static 静态变量**

**static**：修饰符，表示静态，用于修饰成员变量或成员方法。

**静态变量**：静态变量是被static修饰的成员变量，

- 被这个类的所有对象共享
- 不属于对象，属于类
- 随着类的加载而加载，优先于对象存在

**调用方式**：

1. 类名调用（推荐）：`类名.静态变量`
2. 对象名调用：`对象名.静态变量`

**静态变量的内存（静态区）解析**：

- 在 JDK 7 及以前，静态变量存储在方法区，随着类的加载而加载。
- 从 JDK 8 开始，静态变量存储在堆内存。这是为了为了配合GC（垃圾回收），静态对象和普通对象一样可以被正常回收，避免内存泄漏。
- 堆内存中 new 出来的对象空间也存储了静态区的地址。

### 9.2 **static 静态方法**

**静态方法**：静态方法是被static修饰的成员方法，

- 多用于<span style="color: red;">测试类</span>或<span style="color: red;">工具类</span>中
- Javabean类很少会用

**调用方式**：

1. 类名调用（推荐）：`类名.静态变量`
2. 对象名调用：`对象名.静态变量`

**工具类**：不是用来描述一类事物的，也没有main方法，而是帮我们做一些事情的类。

```Java
// 工具类
public class Utils{
    // 私有构造方法，外部无法 new
    private Utils() {
        // 防止内部意外调用（比如反射攻击）
        throw new RuntimeException("工具类不能实例化");
    }
    // 工具类中的方法（静态）
    public static void method1() {
        System.out.println("方法1");
    }
    
    public static void method2() {
        System.out.println("方法2");
    }
}

// 在测试类中调用工具类中的静态方法
public class Test{
    public static void main(String[] args) {
        Utils.method1();
        Utils.method2();
    }
```

> 静态只能调用静态：静态方法只能访问静态变量和其他的静态方法
> 
> 非静态可以调用所有：非静态方法可以访问静态变量和静态方法，以及非静态变量和非静态方法
> 
> 静态方法没有 this 关键字

**重新认识 main 方法**：

```Java
public class Test {
    public static void main(String[] args) {
    }
}
```

- public：被 JVM 调用，访问权限最大
- static：被 JVM 调用，类名访问；测试类中其他方法也是静态的
- void：不需要给 JVM 返回值
- main：固定的名字，被 JVM 识别
- String[ ] args：接收运行时参数

### 9.3 **final 关键字**

**final**：最终的，不可改变的，用于修饰类、方法和变量

- 只能赋值一次，数据不可变
- 名字大写，多个单词下划线隔开

**final 修饰变量**：

```Java
// 定义一个基本数据类型的常量
final int NUMBER = 100

// 定义一个引用数据类型的常量，不能指向其他对象
final Student STU = new Student("zhangsan",23)  // Java不支持关键字传参
// STU = new Student(); // 会报错，不能修改STU存的内存地址
STU.setName("lisi") // 能正常修改对象中的属性

// 如果不想让对象的某个属性发生改变，即没有set方法
// 可以在类中用final修饰成员变量：private final String name = “zhangsan”
```

### 9.4 **枚举**

**枚举**：枚举是一种数据类型，用于定义一组常量。

**使用场景**：用于对象个数是有限个的情况。如订单的状态、月份、星期、游戏角色职业、会议室预约状态、设备状态。

**枚举类的定义**：

```Java
// 枚举类
public enum OrderState {
    // 在枚举类的第一行，把所有的对象都罗列出来了
    PAYMENT_PENDING("待支付"),
    PROCESSING("处理中"),
    SHIPPED("已发货"),
    OUT_FOR_DELIVERY("配送中"),
    COMPLETED("已完成"),
    CANCELED("已取消");

    private final String name;

    private OrderState(String name){
        System.out.println("看看构造方法执行了吗");
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

**枚举对象的创建**：

```Java
public class EnumTest {
    public static void main(String[] args) {

        // 获取枚举类的对象，等价于OrderState o1 = new OrderState("待支付")，但构造函数是私有的
        OrderState o1 = OrderState.PAYMENT_PENDING;
        System.out.println(o1.getName());

        // 匹配
        switch (o1){
            case PAYMENT_PENDING -> System.out.println("待支付");
            case PROCESSING -> System.out.println("处理中");
            case SHIPPED -> System.out.println("已发货");
            default -> System.out.println("其他");
        }

        // 用枚举类自带的values()方法获取枚举类对象数组
        OrderState[] arr = OrderState.values();
        for (OrderState os : arr) {
            System.out.println(os.getName());
        }
    }
}
```

> 注意事项:
> 
> 1. 每一个枚举项，都是该枚举类的对象
> 2. 枚举项底层就是常量，默认用`public static final`修饰
> 3. 枚举类的第一行必须是枚举项，枚举项之间用逗号隔开，以分号作为结尾
> 4. 构造方法必须是`private`，不让外界创建本类的对象, 在使用枚举类时，已经把所有的对象都通过构造方法创建好了
> 5. 编译器会给枚举类新增两个默认存在的方法：`values()`和`valueOf()`

## 十、面向对象高级

### 10.1 **继承**

**面向对象的三大特征**：

1. 封装
2. 继承
3. 多态

**继承**：继承是类与类之间的一种父子关系，Java中提供关键字extends，用于建立类与类之间的关系

**继承的好处**：

- 父类可以抽取子类中的重复代码，提高代码的可复用性
- 子类可以在父类基础上增加其他功能，提高代码的扩展性

```Java
public class 子类 extends 父类 {}
```

**继承结构设计**：当类与类之间，存在相同（共性）的内容，并满足子类是父类中的一种，就可以考虑使用继承，来优化代码

### 10.2 **多态**

### 10.3 **接口**

### 10.4 **内部类**

## 十一、常见 API