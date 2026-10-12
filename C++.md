# C\+\+

# 面向对象如何理解

> C\+\+支持使用一个对象来描述程序，其可以将数据和操作数据的函数放在一起形成一个对象。
> 
> 比如创建一个student类，这个类中包含了姓名，年龄，以及相应的操作函数。通过类创建出来的具体学生A，就是一个对象。
> 
> 面向对象有三个特征：
> 
> 1. 封装：可以将变量和操作变量的函数放在一起，并控制访问权限。比如如果一个变量是私有private的话，那么就只能使用类中的成员函数来改变其数值。隐藏实现细节，不让类以外的程序直接访问或修改类内成员，只能提供访问的公共接口。
> 
> 2. 继承：一个类可以复用另一个类的属性和方法。
> 
> 3. 多态：同一个接口，不同对象有不同的表现。需要看类指针实际指向的地址。运行多态需要三个条件：有继承关系、父类函数加virtual\(virtual函数表示允许子类重写，运行时会根据对象的真实类型决定调用谁\)、用父类指针指向子类对象（Animal\* a = new Dog\(\);）
> 
>     - 多态实用性例子：支付功能，扫完码之后可以选择支付宝、微信、银行卡支付，他们都有一个共同动作，pay\(\);但就是调用的接口不一样，不用多态的话就只能if\(type == "alipay"\)等等，如果后续新增支付方式，就得在pay\(\)函数中增加else if。用了多态后如下，不需要该pay\(\)函数，只需要添加一个新类即可。总结来说就是，现在有这么一个支付功能，有很多支付功能，具体怎么实现我不管，我每次就调用pay\(\)函数，就能实现。
> 
>     ```Plain Text
>     void checkout(Payment* payment) {
>         payment->pay();
>     }
>     
>     int main() {
>         AliPay alipay;
>         WeChatPay wechat;
>         BankPay bank;
>     
>         checkout(&alipay);
>         checkout(&wechat);
>         checkout(&bank);
>     
>         return 0;
>     }
>     ```
> 
>     Q：继承有哪些继承方式
> 
>     A：public、protect、private
> 
>     Q：类对象有哪些初始化方式
> 
>     A：默认构造、有参构造、拷贝构造
> 
> 

# using namespace std

> std是C\+\+标准库的命名空间，using namespace std表示全局展开命名空间，后续代码不用再加std::前缀
> 
> 

# 构造函数

> 对象创建时自动调用的函数，用来初始化对象的
> 
> 比如你定义一个学生类：
> 
> ```Plain Text
> class Student {
> public:
>     string name;
>     int age;
> };
> ```
> 
> 创建对象后还要手动赋值：
> 
> ```Plain Text
> Student s;
> s.name = "张三";
> s.age = 18;
> ```
> 
> 如果用构造函数，可以在创建对象时直接初始化：
> 
> ```Plain Text
> #include <iostream>
> using namespace std;
> 
> class Student {
> public:
>     string name;
>     int age;
> 
>     Student(string n, int a) {
>         name = n;
>         age = a;
>     }
> 
>     void show() {
>         cout << name << " " << age << endl;
>     }
> };
> 
> int main() {
>     Student s("张三", 18);
>     s.show();
> 
>     return 0;
> }
> ```
> 
> 输出：
> 
> ```Plain Text
> 张三 18
> ```
> 
> 上面这个就是构造函数
> 
> 构造函数的特点：
> 
> 1. 构造函数名字必须与类名一致
> 
> 2. 构造函数没有返回值，连void都不能写
> 
> 3. 创建对象时会自动调用
> 
> 4. 构造函数可以重载，也就是可以写多个参数不同的构造函数（如下所示）
> 
> ```C++
> class Student {
> public:
>     string name;
>     int age;
> 
>     Student() {
>         name = "未知";
>         age = 0;
>     }
> 
>     Student(string n, int a) {
>         name = n;
>         age = a;
>     }
> };
> 
> Student s1;              // 调用无参构造函数
> Student s2("李四", 20);  // 调用有参构造函数
> ```
> 
> 拷贝构造函数：当用一个对象创建另外一个对象的时候，会调用拷贝构造函数
> 
> ```TypeScript
> class Student {
> public:
>     string name;
> 
>     Student(string n) : name(n) {}
> 
>     Student(const Student& other) {
>         name = other.name;
>         cout << "拷贝构造" << endl;
>     }
> };
> 
> Student s1("张三");
> Student s2 = s1;  //这里s2 = s1不是普通赋值，而是用s1创建s2，所以会调用拷贝构造
> ```
> 
> 派生类构造顺序：
> 
> ```Java
> //假设有继承关系
> class Animal {
> public:
>     Animal() {
>         cout << "Animal 构造" << endl;
>     }
> };
> 
> class Dog : public Animal {
> public:
>     Dog() {
>         cout << "Dog 构造" << endl;
>     }
> };
> 
> //创建
> Dog d;
> 
> //输出：
> Animal 构造
> Dog 构造
> 
> //也就是说会先构造基类，再构造派生类
> ```
> 
> 成员对象：一个类里面包含另一个类对象
> 
> ```Java
> class Engine {
> public:
>     Engine() {
>         cout << "Engine 构造" << endl;
>     }
> };
> 
> class Car {
> private:
>     Engine engine;
> 
> public:
>     Car() {
>         cout << "Car 构造" << endl;
>     }
> };
> 
> //创建
> Car car;
> 
> //输出
> Engine 构造
> Car 构造
> 
> //更完整的顺序是：
> //1.先构造基类
> //2.再构造成员对象
> //3.最后执行派生类的构造函数
> //对于析构函数来说是反过来的
> ```
> 
> Q：移动构造函数
> 
> A：用一个即将被丢弃的对象的资源，直接构造新对象，转移资源所有权，特点右值引用Type\&\&
> 
> Q：类对象初始化方式
> 
> A：默认初始化、直接初始化、拷贝初始化、列表初始化
> 
> 

# 析构函数

> 对象销毁时自动调用的函数，用来做清理工作
> 
> ```C++
> #include <iostream>
> using namespace std;
> 
> class Student {
> public:
>     Student() {
>         cout << "构造函数：对象创建了" << endl;
>     }
> 
>     ~Student() {
>         cout << "析构函数：对象销毁了" << endl;
>     }
> };
> 
> int main() {
>     Student s;
> 
>     cout << "main 函数执行中" << endl;
> 
>     return 0;
> }
> ```
> 
> 其中`~Student()`就是析构函数
> 
> 析构函数特点：
> 
> 1. 名字是类名前面加\~
> 
> 2. 析构函数没有返回值，连void也不能写
> 
> 3. 销毁对象时自动调用
> 
> 4. 一个类只能有一个析构函数
> 
> 虚析构函数：基类析构函数前加virtual，保证父类指针指向子类对象时析构完整
> 
> 如果一个类需要作为基类，并且可能通过基类指针删除派生类对象，那么基类析构函数就必须写成virtual
> 
> Q：什么时候对象会被销毁？
> 
> A：
> 
> ```Plain Text
> void test() {
>     Student s;
> } // 函数结束，s 被销毁，自动调用析构函数
> ```
> 
> ```Plain Text
> Student* p = new Student;
> delete p; //执行delete p时会调用析构函数
> ```
> 
> Q：为什么需要虚析构函数？
> 
> A：因为析构时需要根据真实的对象类型，从子类到父类完整清理。不加virtual的话，编译器只看指针类型`Animal* a`，所以这个时候`delete a`就会被当成父类的析构函数，这样的话Dog里申请的内存资源就泄露了。加上了virtual关键字，析构函数变为虚函数，delete就会根据对象的真实类型去找析构函数。
> 
> 

# 虚函数

> C\+\+为了实现多态，会给有虚函数的类分配一个虚函数表
> 
> 虚函数依赖vptr和vtable实现。this\-\>vptr\-\>vtable\-\>virtual fun。（静态成员函数没有this指针，所以不能被virtual修饰）
> 
> 虚函数是一种动态多态，在运行时实现的\.与之对应的静态多态是在编译期间实现的
> 
> 允许子类重写，并通过父类指针实现多态
> 
> ```C++
> class Animal {
> public:
>     virtual void speak() {
>         cout << "Animal speak" << endl;
>     }
> 
>     virtual void run() {
>         cout << "Animal run" << endl;
>     }
> };
> ```
> 
> ```C++
> Animal 虚函数表
> ----------------
> speak -> Animal::speak
> run   -> Animal::run
> 
> //Animal对象里面还会放一个指针，vptr，指向这张表
> ```
> 
> ```C++
> //派生类重写虚函数
> class Dog : public Animal {
> public:
>     void speak() override {
>         cout << "Dog speak" << endl;
>     }
> };
> ```
> 
> ```C++
> Dog 虚函数表
> ----------------
> speak -> Dog::speak
> run   -> Animal::run
> ```
> 
> ```C++
> //当你写
> Animal* a = new Dog();
> a->speak();
> 
> //表面看a是Animal* ， 但实际指向的是Dog对象
> //这个Dog对象里面的vptr指向Dog的虚函数表
> //所以最后的执行过程就是通过a找到Dog对象，通过Dog对象里的vptr找到Dog虚函数表
> //，在表里找到speak对应的函数地址，调用Dog::speak
> ```
> 
> 所以虚函数表的作用就是，父类指针只知道你是Animal类，但是通过vptr可以查到对象的真实类型Dog，然后调用对应版本的函数
> 
> 

# this、vptr和vtable

> this指针指向当前对象，通过this指针可以访问对象内部的vptr，vptr再指向对象实际类型对应的vtable，虚函数调用的时候通过虚表找到对应的函数地址，实现多态
> 
> `vptr` 本身通常就是一个地址：
> 
> ```Plain Text
> Derived对象
> ┌──────────────┐
> │ vptr         │ ─────→ Derived vtable
> ├──────────────┤
> │ Base成员      │
> ├──────────────┤
> │ Derived成员   │
> └──────────────┘
> ```
> 
> 假设：
> 
> ```Plain Text
> class Base {
> public:
>     virtual void f1();
>     virtual void f2();
> };
> 
> class Derived : public Base {
> public:
>     void f1() override;
>     virtual void f3();
> };
> ```
> 
> 那么概念上的虚表可能是：
> 
> ```Plain Text
> Base vtable
> ┌─────────────────┐
> │ Base::f1        │
> │ Base::f2        │
> └─────────────────┘
> 
> 
> Derived vtable
> ┌─────────────────┐
> │ Derived::f1     │ ← 覆盖父类f1
> │ Base::f2        │ ← 没有重写，继续用父类版本
> │ Derived::f3     │ ← 派生类新增虚函数
> └─────────────────┘
> ```
> 
> 多继承的话，一个派生类对象会有多个vptr
> 
> 

# 静态成员函数

> 用static修饰的类成员函数，不属于某个对象，属于整个类
> 
> ```C++
> //直接用类调用，不用创建对象
> Person::showCount();
> ```
> 
> Q：静态成员变量什么时候初始化？
> 
> A：进入main之前
> 
> Q：静态成员函数与普通成员函数区别
> 
> A：是否存在this指针
> 
> 

# const常量

> 1. 程序运行过程中不会被修改的数据
> 
> 2. 只读输入参数，函数内部只使用、不修改
> 
> 项目中用到的地方：
> 
> 路径字符串和摄像头帧数据作为函数参数的时候
> 
> ```C++
> int lcd_show_jpeg_file(lcd_device_t *lcd, const char *jpeg_path);
> int lcd_show_rgb565_data(lcd_device_t *lcd,
>                          const unsigned char *rgb565_data,
>                          int data_size);
> ```
> 
> 驱动里的文件操作表
> 
> ```C
> static const struct file_operations xxx_fops = {
>     .owner = THIS_MODULE,
>     .open = xxx_open,
>     .write = xxx_write,
>     .release = xxx_release,
> };
> ```
> 
> 设备树匹配表
> 
> ```C++
> static const struct of_device_id ov5640_of_match[] = {
>     { .compatible = "ovti,ov5640" },
>     { }
> };
> ```
> 
> Q：`const`修饰指针时，“指针常量” 与 “常量指针” 的本质差异是什么
> 
> A：const修饰的是指针本身还是指针指向的数据。指针常量int \*const p = \&a;指针不能改，但是\*p指向的值可以改，这个在项目中用的很多，传入函数参数的时候只读不改，这就是指针常量的典型使用方法。常量指针 const int \*p = \&a；p指针可以改，但是不能通过p去修改所指向的值。
> 
> Q：const修饰的常量真的不能改吗？
> 
> A：局部const常量的时候，此时const只是编译器限制，可以通过指针强行改。但是全局const常量是放在只读段中，是无法修改的
> 
> 

# static

> 用来控制变量的存储方式和作用范围的
> 
> static关键字没有赋值时，会默认赋值0
> 
> 静态局部变量：只需要初始化一次，下次调用时保持上次的值，仅在定义它的函数里可见。存储位置也会被放到数据段中。
> 
> 静态全局变量：当前源文件内可见，外部文件无法extern引用该变量
> 
> 静态函数：外部文件无法引用该函数
> 
> C\+\+中还有静态成员变量（程序启动阶段，main函数执行之前完成初始化）,整个类共享一份内存。
> 
> ```C++
> class Person
> {
> public:
>     static int total; // 声明，所有对象共用
>     int age;
> };
> int Person::total = 0; // 类外定义初始化
> 
> Person p1, p2;
> p1.total++;
> cout << p2.total; // 输出 1，共享同一份数据
> ```
> 
> 

# 指针数组和数组指针

> 指针数组：int \*p\[3\]，是个数组，每个元素都是指针。在V4L2 mmap的多缓冲用到了。buffers是一个指针数组，里面的每个元素都是一个指针，指向第x块视频缓冲区的地址。
> 
> 数组指针：int \(\*p\)\[3\] ， 是个指针，指向含有三个int的数组
> 
> 

# 内存泄漏的原因

> malloc申请了内存但是在所有执行路径上没有释放
> 
> Q：MCU上用malloc还是静态数组
> 
> A：用静态数组
> 
> ```C
> static uint8_t can_buf[64];
> //其内存大小编译期确定
> //malloc灵活但是不可控，容易失败和实时性问题
> ```
> 
> 

# 堆和栈的基本概念和区别

> 都是程序运行时使用的内存区域。
> 
> 堆是程序员手动malloc/free申请释放的。
> 
> 栈是编译器自动管理的，主要存放局部变量，函数参数，临时数据。
> 
> Q：如何测定栈的最大使用深度？
> 
> A：栈填充，把整块栈都填成固定的数，然后从栈底扫描。
> 
> 

# 多态

> 同一个接口调用，在运行时根据对象的真实类型，执行不同的函数实现
> 
> 多态主要靠虚函数表vtable\+虚表指针vptr
> 
> 成立条件：
> 
> 1. 有继承关系
> 
> 2. 基类函数声明为virtual
> 
> 3. 派生类重写该虚函数
> 
> 4. 使用基类指针或引用调用
> 
> ```C++
> class Animal {
> public:
>     virtual void speak() {
>         cout << "Animal" << endl;
>     }
> };
> 
> class Dog : public Animal {
> public:
>     void speak() override {
>         cout << "Dog" << endl;
>     }
> };
> 
> Animal *p = new Dog;
> p->speak();   // 调用 Dog::speak()
> ```
> 
> Q：为什么普通成员函数不行？
> 
> A：因为普通成员函数在编译的时候就确定调用哪个函数了，而虚函数调用是在运行时根据对象的真实类型决定。
> 
> 

# Volatile 

> 使用场景：禁止编译器优化，强制CPU每次读写都访问原始寄存器地址，不适用CPU缓存中的值
> 
> 属于硬件外设的寄存器，其值会被硬件自动修改，编译器会对普通变量做读写缓存优化（把值存在CPU缓存中，不重复读硬件地址），volatile强制CPU每次直接访问物理地址，禁止编译器优化，是操作外设寄存器必不可少的修饰符
> 
> 

# Inline

> 内联函数
> 
> 作用：
> 
> 1. 建议编译器内联展开
> 
> 2. 内联函数被多个\.cpp文件包含而不导致重复定义错误
> 
> 用处：被频繁调用的短小函数中
> 
> Q：Inline和宏的区别
> 
> A：宏是在预处理阶段就进行文本替换了，不做类型检查，其参数求值可能多次。inline不一定会被替换，只是建议，更安全，参数求值只会进行一次。
> 
> 

# 链表

> 链表的各个节点在堆内存里随机分配，依靠指针串联。指针保存上一节点和下一节点的地址
> 
> Q：数组和链表的区别
> 
> A：数组是连续内存空间，链表是非连续内存
> 
> Q：什么时候用数组？什么时候用链表？
> 
> A：数组适合读的多，改结构少，需要快速随机访问的。链表适合频繁插入删除，不要求随机访问的。我们项目中使用链表而不用动态数组原因是动态数组头部插入效率低，底层是连续内存，插入后需要把所有元素整体向后移动
> 
> 

# 栈

> ARM中是满递减栈（FD），SP指向最后一个入栈数据
> 
> ARM中有两个栈指针，一个是MSP主栈指针，一个是PSP进程栈指针，二者都映射到R13，同一时刻只有一个是活跃的SP
> 
> 压栈出栈就是对内存和SP指针的操作，软硬件都有。
> 
> 

# 结构体内存对齐概念，如何通过重排字段省内存

> 对于一个结构体来说，其成员不是一个一个挨个放的，编译器会按照对齐要求插入padding
> 
> ```C++
> //对齐规则
> /*
>     1. 每个成员都要放在整个
> */
> char   对齐 1 字节
> short  对齐 2 字节
> int    对齐 4 字节
> float  对齐 4 字节
> double 对齐 8 字节
> 指针    32位对齐4字节，64位对齐8字节
> 
> struct A {
>     char c;
>     int i;
>     char d;
> };
> //sizeof(struct A) = 12
> 
> //重排字段后
> struct A2 {
>     int i;
>     char c;
>     char d;
> };
> //sizeof(struct A2) = 8
> 
> //所以重排字段的目的就是减少padding
> ```
> 
> Q：为什么需要对齐？
> 
> A：CPU按照地址对齐访问速度更快，有些架构访问未对齐的数据还可能异常
> 
> 

# 结构体和联合体的区别，联合体在嵌入式中的应用

> 结构体是每个成员各自占各自内存，总大小等于所有成员大小加上padding
> 
> 联合体是每个成员共享一块内存，总大小等于最大成员大小
> 
> 常用于按字节访问一个多字节数据
> 
> ```C++
> typedef union {
>     uint32_t word;
>     uint8_t byte[4];
> } U32Bytes;
> 
> U32Bytes data;
> data.word = 0x12345678;
> 
> printf("%02X\n", data.byte[0]);
> //串口通讯里经常用，把32位数据拆成4个字节发送
> //节省内存的时候用
> ```
> 
> 

# 位域

> 让结构体成员只占指定的几个bit，而不是完整的1/2/4字节
> 
> 比如有一个 8 位数据：
> 
> ```Plain Text
> bit7 bit6 bit5 bit4 bit3 bit2 bit1 bit0
> ```
> 
> 协议规定：
> 
> ```Plain Text
> bit0       开关 enable
> bit1~2     模式 mode
> bit3       错误 error
> bit4~7     版本 version
> ```
> 
> 如果收到一个字节：
> 
> ```Plain Text
> uint8_t v = 0b10110101;
> ```
> 
> 按协议拆：
> 
> ```Plain Text
> bit7 bit6 bit5 bit4 bit3 bit2 bit1 bit0
>  1    0    1    1    0    1    0    1
> ```
> 
> 所以：
> 
> ```Plain Text
> enable  = bit0       = 1
> mode    = bit1~2     = 0b10 = 2
> error   = bit3       = 0
> version = bit4~7     = 0b1011 = 11
> ```
> 
> 手动写就是：
> 
> ```Plain Text
> enable  = (v >> 0) & 0x01;
> mode    = (v >> 1) & 0x03;
> error   = (v >> 3) & 0x01;
> version = (v >> 4) & 0x0F;
> ```
> 
> 而位域是想让你这样写：
> 
> ```Plain Text
> typedef struct {
>     unsigned enable  : 1;
>     unsigned mode    : 2;
>     unsigned error   : 1;
>     unsigned version : 4;
> } Status;
> ```
> 
> Q：跨字节的位域
> 
> A：
> 
> ```C++
> struct A {
>     unsigned a : 3;
>     unsigned b : 5;
>     unsigned c : 6;
> };
> //这样的话就会有个问题，字段跨字节后会存在是低往高排还是高往低排
> ```
> 
> 

# 内存碎片

> 总空闲内存看起来够，但是被切成很多小块，导致申请一大块连续内存失败
> 
> 比如堆内存一开始是：
> 
> ```Plain Text
> [ 空闲 1000 字节 ]
> ```
> 
> 运行一段时间后，反复 `malloc/free`：
> 
> ```Plain Text
> [已用100][空闲50][已用200][空闲80][已用150][空闲70]
> ```
> 
> 总空闲：
> 
> ```Plain Text
> 50 + 80 + 70 = 200 字节
> ```
> 
> 但如果这时你要：
> 
> ```Plain Text
> malloc(180);
> ```
> 
> 可能失败，因为没有一块连续的 180 字节。
> 
> 这就是内存碎片。
> 
> Q：为什么嵌入式很怕内存碎片？
> 
> A：系统长期运行才会出现，不能靠重启解决，bug难复现。解决方法就是少用malloc/free
> 
> 

# 野指针和悬空指针

> 野指针是指针没有被正确初始化
> 
> 悬空指针是原来指向一块有效内存，但那块内存被释放了
> 
> 空指针是指针等于NULL
> 
> Q：指针大小
> 
> A：32位系统是4字节，64位系统是8字节
> 
> Q：int \*p\[10\]和int \(\*p\)\[10\]区别
> 
> A：第一个是指针数组，数组中的每个元素都是int \*指针。第二个是数组指针，指向10个int数组的数组指针
> 
> 

# 函数指针

> 函数指针是指向函数的指针，保存函数的入口地址
> 
> 比如普通函数：
> 
> ```Plain Text
> int add(int a, int b) {
>     return a + b;
> }
> ```
> 
> 定义一个函数指针：
> 
> ```Plain Text
> int (*fp)(int, int);
> ```
> 
> 含义是：
> 
> ```Plain Text
> fp 是一个指针
> 它指向的函数：返回 int，参数是两个 int
> ```
> 
> 赋值：
> 
> ```Plain Text
> fp = add;
> ```
> 
> 调用：
> 
> ```Plain Text
> int ret = fp(1, 2);
> ```
> 
> Q：典型应用
> 
> A：回调函数和驱动接口表
> 
> Q：项目中使用回调函数的原因
> 
> A：统一接口，降低耦合，方便后续的扩展和维护
> 
> 

# 回调函数

> 回调函数就是把一个函数作为参数传给别人
> 
> 比如：
> 
> ```Plain Text
> #include <stdio.h>
> 
> void run(void (*callback)(void)) {
>     printf("run start\n");
> 
>     callback();
> 
>     printf("run end\n");
> }
> 
> void hello(void) {
>     printf("hello callback\n");
> }
> 
> int main() {
>     run(hello);
> 
>     return 0;
> }
> ```
> 
> 输出：
> 
> ```Plain Text
> run start
> hello callback
> run end
> ```
> 
> 这里：
> 
> ```Plain Text
> run(hello);
> ```
> 
> 

# 大小端

> 大小端含义就是一个多字节数据在内存里，低地址先放高字节还是低字节
> 
> 大端就是低地址放高字节
> 
> 小端就是低地址放低字节
> 
> Q：如何判断机器的大小端？
> 
> A：用char\*看最低地址的那个字节
> 
> 

# 同步异常和中断区别

> 同步异常就是由当前执行的指令直接引起，常见就是访问非法地址
> 
> 中断是外部的事件打断当前的执行流
> 
> 

# SVC调用的基本格式

> SVC（Supervisor Call）主管调用
> 
> ARM架构里就是主动触发同步异常的指令，用来让用户程序进入内核
> 
> 常用于：启动第一个任务
> 
> ```JSON
> //SVC调用基本格式
> mov x8, #syscall_number   // 系统调用号
> mov x0, #arg0             // 第 1 个参数
> mov x1, #arg1             // 第 2 个参数
> mov x2, #arg2             // 第 3 个参数
> mov x3, #arg3             // 第 4 个参数
> mov x4, #arg4             // 第 5 个参数
> mov x5, #arg5             // 第 6 个参数
> svc #0                    // 进入内核
> ```
> 
> 

# 智能指针

> 自动管理堆内存生命周期的工具
> 
> 常见有三个智能指针
> 
> unique\_ptr：独占所有权
> 
> shared\_ptr：共享所有权（多个shared\_ptr指向同一堆内存，内部维护引用计数器，引用次数==0，自动释放堆内存）
> 
> weaked\_ptr：配合shared\_ptr的，我知道这个对象在哪里，但是我不拥有它，不会增加shared\_ptr的引用计数
> 
> 

# 左值和右值

> 左值有稳定身份和存储位置的对象
> 
> 右值是临时值或即将失效的对象
> 
> 判断：有名字，能取地址的通常是左值，临时值、计算结果通常是右值
> 
> 

# RALL机制

> RALL全称就是资源获取即初始化
> 
> 原理就是把资源的申请放在构造函数，资源的释放放在析构函数
> 
> 

# 内存分布

> 1. \.text代码段：编译后的机器指令，只读
> 
> 2. \.rodata只读数据段：字符串常量，const只读数据
> 
> 3. \.data已初始化数据段：存放已经初始化并且初始值不为0的全局变量和静态变量
> 
> 4. \.bss未初始化数据段：存放未初始化或者初始值为0的全局变量和静态变量
> 
> 5. heap堆：动态内存分配
> 
> 6. stack栈：保存函数参数、局部变量、返回地址、以及函数调用现场
> 
> 

# 加载地址和运行地址

> 加载地址：程序存放在哪里
> 
> 运行地址：CPU最终从哪个地址取值运行
> 
> 如果加载地址和运行地址不一样的话就需要进行**重定位**，修改相关地址之后再跳转运行
> 
> 

# 浮点数

> 1位符号位\+8位指数位\+23位尾数位
> 
> 符号S\|指数E\|尾数M
> 
> Q：浮点数可以直接比大小吗？
> 
> A：编译器是支持的，但是由于浮点数在计算机中用二进制近似表示，很多十进制小数不能被精确表示，所以计算后的浮点结果是有微小误差的。有可能两个理论上相等的浮点数，由于精度误差，实际的存储值可能不一样，所以不建议用==直接判等，一般都会用误差范围比较，就是两个浮点数的差值是否小于一个允许误差。
> 
> 

# AMP和SMP

> AMP（Asymmetric Multi\-Processing，非对称多处理），就是多个处理器核心之间分工不同，每个核心都可以运行不同的软件环境
> 
> SMP（Symmertic Multi\-Processing，对称多处理），就是多个处理器运行同一个操作系统
> 
> 

# 可执行文件

> ELF
> 
> \.bin
> 
> \.hex
> 
> 

# 数据结构

> 链表：内存不连续，节点靠指针相连，没有随机访问
> 
> 队列：逻辑数据结构，先进先出FIFO
> 
> 数组：连续内存，支持随机访问，读下标
> 
> 

# 字节流数据的接收

> 接收\-\>缓存\-\>解析
> 
> 先接收到一串字节数组，然后缓存数据，因为字节流没有天然的数据边界，可能一次收到多包，一次收到半包，所以通常会放进一个环形缓冲区或者消息队列中，解析任务就可以从这些buffer中读取，解析任务会设计协议进行解析，帧头、长度、有效数据、校验。
> 
> Q：如何避免字节流数据丢包？
> 
> A：使用环形缓冲区，防止接收任务不断写，解析任务处理不过来。此外，在发送前需要设计可靠的数据协议，帧头、长度、有效数据、校验。或者就是增加确认机制，接收到了正确数据返回ACK
> 
> 

# 无符号与有符号做运算

> 有符号int会被隐式转换为unsigned无符号类型
> 
> 

# \#pragma pack\(1\)

> 按照一字节对齐，取消内存对齐
> 
> 

# 宏定义

> 宏是简单的文本替换，很容易因为运算符优先级产生错误
> 
> `#define SQUARE(x) x*x`：如果传入的是SQUARE\(1\+2\)就会出现1\+2\*1\+2❌
> 
> `#define SQUARE(x) (x)*(x)`：传入1\+2不会出错，但是如果是类似于sizeof SQUARE\(x\)这种就会出错❌
> 
> `#define SQUARE(x) ((x)*(x))`：这种才是正确的
> 
> 并且宏定义末尾不要加分号，宏替换会把分号带入代码的
> 
> 

# Sizeof

> sizeof返回的是字节数，返回类型是size\_t
> 
> - 数组作为函数参数会退化为指针
> 
> ```C
> void func(int a[])
> {
>     sizeof(a);   // 实际是 sizeof(int *)
> }
> //所以不能在函数里里面使用sizeof(a)来得到数组的占用字节数
> ```
> 
> - 字符串
> 
> ```C
> char a[] = "hello";
> sizeof(a);       // 6，包括 '\0'
> strlen(a);       // 5，不包括 '\0'
> 
> //但是
> char *p = "hello";
> sizeof(p);       // 指针大小
> strlen(p);       // 5
> ```
> 
> - sizeof一般不会执行表达式
> 
> ```C
> int a = 10;
> 
> sizeof(a++);
> printf("%d", a);
> 
> //通常a还是10
> ```
> 
> - 阴人的C语言题目
> 
> ```C
> sizeof('a')
> //C语言中字符常量的类型是int
> //C++中的'a'类型是char
> ```
> 
> 

# register关键字

> 建议编译器将变量放到寄存器中，而不是内存中，加快访问速度
> 
> 

# auto关键字

> lambda最常见的接收方式就是auto，auto会自动推导出lambda对象的类型，然后保存到变量中
> 
> Q：lambda表达式机制
> 
> A：编译器自动生成一个匿名函数对象，捕获的变量会成为对象内部的成员变量，再通过operator\(\)实现函数调用
> 
> 例如：
> 
> ```Plain Text
> int x = 10;
> 
> auto f = [x](int y) {
>     return x + y;
> };
> ```
> 
> 使用：
> 
> ```Plain Text
> cout << f(5);   // 15
> ```
> 
> 编译器可以概念化成：
> 
> ```Plain Text
> class __Lambda {
> private:
>     int x;
> 
> public:
>     __Lambda(int _x) : x(_x) {}
> 
>     int operator()(int y) const {
>         return x + y;
>     }
> };
> ```
> 
> 然后：
> 
> ```Plain Text
> __Lambda f(x);
> f(5);
> ```
> 
> 捕获机制很重要，值捕获后，相当于再lambda对象内部保存了一份值的副本，外面的值后面变化不会影响之前捕获进去的值
> 
> Q：lambda作用
> 
> A：STL、回调函数
> 
> Q：lambda与函数指针的区别
> 
> A：函数指针只保存函数地址，有捕获的lambda还需要保存对象变量
> 
> 

# 可重入函数

> 一个函数能同时被多个任务同时调用
> 
> 注意事项：用局部变量保存数据
> 
> 

# malloc

> ```C
> | metadata | user data |
>              ↑
>            malloc返回
> //malloc申请的内存块不只有数据，还有metadata，会保存当前块大小，是否空闲，前后块信息，空闲链表指针
> ```
> 
> malloc拿到的首先是虚拟地址，实际物理页常在第一次访问时通过缺页异常按需分配
> 
> VMA（Virtual Memory Area , 虚拟内存区域）：是进程空间上一段连续的虚拟内存空间
> 
> brk：修改堆对应的VMA的结束地址
> 
> mmap：内核新建一个VMA
> 
> 注意：VMA仅仅管理虚拟地址，并没有分配物理页，只有进程访问这个虚拟地址，触发缺页异常，内核才会分配物理页，建立页表映射
> 
> malloc申请较小的内存优先从malloc自己管理的headp中分配，申请较大的内存倾向于直接mmap。临界值经验为128KB
> 
> malloc是用户态分配器，优先复用已有的heap，不足时通过brk或者mmap向内核申请更多虚拟地址空间，物理页通常在真正访问时通过缺页异常机制按需分配
> 
> Q：大内存申请倾向于mmap原因
> 
> A：减少堆碎片
> 
> 比如 heap：
> 
> ```Plain Text
> 低地址
> | A 10KB | B 500KB | C 20KB | D 300KB |
> 
> 如果
> free(B)
> 
> 变成了
> | A | 500KB空洞 | C | D |
> 
> 虽然B空闲了，但是A和C还在
> 
> 也就是500KB内存不能通过brk把这块内存还给操作系统
> 容易形成堆碎片
> ```
> 
> Q：brk扩展堆后，malloc如何从堆中分配
> 
> A：brk只负责把heap的虚拟地址范围扩大，之后由malloc自己把这片空间切成一个个chunk来管理
> 
> Q：释放内存会发生什么
> 
> A：free只是把内存还给malloc分配器，不是立即还给操作系统，被释放的内存缓存在用户态，不够了就向内核申请更多
> 
> Q：缺页异常
> 
> A：第一次访问虚拟地址的时候，CPU查页表发现，这个虚拟地址合法但是没有当前的物理页，会触发缺页异常，CPU会转入内核处理，检查这个地址是否属于合法的VMA，合法的话就建立页表映射，分配物理页。所以malloc是用户态分配器
> 
> Q：底层实现原理
> 
> A：从进程的堆内存里找一块足够大的空闲区域，并返回这块区域的首地址
> 
> Q：现有堆内存空间不够用，如何解决？
> 
> A：小块内存的话，使用brk扩展heap。大块内存的话，通过mmap申请虚拟内存区域
> 
> Q：malloc\(0\)
> 
> A：可能返回一个NULL，也可能返回一个不能解引用，但是可以free的非空指针
> 
> Q：kmalloc/kzalloc
> 
> A：物理地址连续，kzalloc会清零内存
> 
> Q：vmalloc
> 
> A：仅虚拟地址连续
> 
> Q：free怎么知道释放多大
> 
> A：metadata中找chunk大小
> 
> 

# 

# 1

