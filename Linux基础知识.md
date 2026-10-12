# Linux基础知识

# linux中cmake构建代码好处？为什么可以跨平台

> cmake是用来生成构建文件的，不直接参与代码的编译。根据CMakelist\.txt文件生成对应的makefile、visual studio等工程
> 
> cmake会根据当前的系统生成对应的构建文件
> 
> 构建文件比如Makefile将如何编译、链接、清理、安装、测试的命令写成规则，让Make自动执行
> 
> 

# 微机原理

> CPU由运算器、控制器、寄存器这些器件组成，这些器件通过总线相连接。
> 
> 运算器负责信息处理
> 
> 寄存器负责信息存储
> 
> 控制器负责控制各种器件进行工作
> 
> 特殊寄存器：
> 
> - IP指令指针寄存器，存放下一条指令的偏移地址
> 
> - FLAGS状态寄存器
> 
> - 段寄存器：CS代码段寄存器（\.text）、SS堆栈段寄存器、DS数据段寄存器（\.data\+\.bss）、ES附加段寄存器
> 
> - SP堆栈指针寄存器：保存当前栈顶的寄存器
> 
> - BP基址指针寄存器：用于访问堆栈中的数据
> 
> 

# 嵌入式linux启动流程

> 芯片上电后，CPU先会运行片上的BootRom，BootRom会根据配置选择启动介质，比如是EMMC还是SD卡。然后从介质中加载第一段启动程序，第一段启动代码进行最基础的硬件初始化，比如设置栈、初始化时钟和DDR。DDR可用之后，加载并运行完整的Uboot，继续初始化各种外设，加载Linux内核、设备树dtb及根文件系统，并将启动参数传递给内核，跳转到内核启动入口。linux内核启动后，进行驱动、内存管理、文件系统等的初始化，最后挂载根文件系统，启动第一个用户空间进程Init。
> 
> 

# UBOOT头部信息

> 1. Image Vector Table\(IVT\):告诉bootrom把uboot加载到DDR的哪个位置
> 
> 2. Boot Data：uboot在SD卡的哪个位置
> 
> 3. Device Configuration Data\(DCD\)：最基础硬件初始化（时钟、DDR）
> 
> 

# 系统底层移植与构建

[基于ZD\-Alpha开发板的系统构建](https://icnrhw376fx6.feishu.cn/wiki/LnozwWecii7ifNkUqtncrGwinMf)

# Linux驱动开发核心

1. 设备模型与硬件资源管理

> 作用：让linux知道这是什么设备，有哪些硬件资源，由哪个驱动负责
> 
> 设备模型负责device和drive的匹配，硬件资源负责从设备树获取GPIO、Clock等资源，并在probe中完成初始化
> 
> 裸机里一个宏定义就可以，Linux里通过设备树来描述硬件，驱动通过compatible来匹配设备，驱动里获取资源（本质就是使用Linux统一的API来解析设备树里的硬件资源属性，然后将软件驱动和真实硬件绑定起来，并拿到可操作的句柄）
> 
> 

2. 内存管理

> 核心作用：虚拟地址管理（页表＋MMU）、进程间内存隔离和保护（页表项记录权限）、需求分页（访问时再缺页异常陷入内核分配物理页）、共享内存
> 
> 

3. 资源调度

> 驱动如何协调硬件资源在不同请求、不同执行上下文之间安全高效地工作
> 
> ```C
> 用户QBUF
> ↓
> 驱动把buffer交给DMA
> ↓
> Camera写buffer
> ↓
> IRQ
> ↓
> buffer标记DONE
> ↓
> DQBUF返回用户
> ```
> 
> 

4. 文件系统接口与用户空间交互

> linux下一切皆文件
> 
> 驱动会把硬件暴露成设备节点，用户程序通过系统调用设备节点操作寄存器
> 
> 

# 原子操作

> 一个操作要么完整执行完，要么完全不执行，不会被其他线程中途打断
> 
> 一个简单的例子：`count++`。这个在C代码层面就是一条语句，但是在CPU层面就是先从内存中读取count到寄存器，寄存器＋1，把结果写回内存。当多个线程同时执行的时候就有可能出现数据竞争。最常见的就是使用互斥锁来实现原子操作，使得同一时间只能有一个线程进入临界区（共享变量）
> 
> 

# gdb调试

> 第一次进入相册的时候只显示背景，没有显示第一张图片，点击下一张后才显示。
> 
> 使用gdb调试，重新交叉编译，加上\-g生成调试信息参数，启动gdb
> 
> `gdb ./imageplayer`，在view\_gallery\(\)和gallery\_get\_current\(\)函数下断点，run运行，查看相册链表状态
> 
> print g\_gallery\.image\_count
> 
> print g\_gallery\.head
> 
> print g\_gallery\.current
> 
> 发现图片count是正确的，说明图片已经正确加载进链表，链表里确实有图片的节点，但是当前的current == head说明进入相册的时候没有把current节点指向头节点的next节点，加上即可。重新编译运行发现currenr \!= head。 
> 
> 另一个调试案例：
> 
> 运行相机预览程序后，程序不崩溃，摄像头也能打开，但是LCD显示图像花屏，位置也不对
> 
> 解决过程：
> 
> 1. 确认摄像头、I2C、framebuffer、CSI这些是否注册成功
> 
> ```Plain Text
> dmesg | grep -i "ov5640\|csi\|video\|fb\|i2c"
> ```
> 
> 期望输出类似：
> 
> ```Plain Text
> imx-i2c 21a0000.i2c: IMX I2C adapter registered
> ov5640 1-003c: ov5640 detected
> mx6s-csi 21c4000.csi: registered /dev/video0
> mxc_sdc_fb fb0: registered framebuffer device
> ```
> 
> 说明 I2C 控制器、OV5640、CSI/V4L2、LCD framebuffer 都被内核识别了。
> 
> 2. 看设备节点是否存在
> 
> ```Plain Text
> ls /dev/video*
> ls /dev/fb*
> ```
> 
> 期望输出：
> 
> ```Plain Text
> /dev/video0
> /dev/fb0
> ```
> 
> 说明应用层可以通过 `/dev/video0` 访问摄像头，通过 `/dev/fb0` 访问 LCD。
> 
> 3. 看摄像头实际输出的格式
> 
> ```Plain Text
> v4l2-ctl -d /dev/video0 --all
> ```
> 
> 期望输出中重点看：
> 
> ```Plain Text
> Format Video Capture:
>     Width/Height      : 640/480
>     Pixel Format      : 'MJPG'
>     Field             : None
> ```
> 
> 4. 看framebuffer的参数
> 
> ```Plain Text
> cat /sys/class/graphics/fb0/bits_per_pixel
> ```
> 
> 期望输出：
> 
> ```Plain Text
> 32
> ```
> 
> **实际调试案例：**
> 
> 现象：运行摄像头程序就segmentation fault（程序试图访问一段不允许访问的地址，操作系统直接杀死进程）
> 
> 解决过程：先在关键路径上增加debug调试日志，看大概程序死在哪一段（memcpy），看dmesg，执行`dmesg | tail -20`，看到
> 
> ```C
> camera_app[1265]: segfault at b6f20000
> ip b6e81234 sp befff120 error 6
> in libc-2.23.so[b6e00000+160000]
> 
> //说明linux内核确认这个进程出现了非法内存访问，但是还是不能定位根因
> ```
> 
> 接着用top看当前进程是否已经崩溃。接着就使用gdb来进行调试，重新编译`gcc -g -O0 camera.c -o camera_app`，启动gdb`gdb ./camera_app`，然后`run`,运行几秒后发现
> 
> ```C
> Program received signal SIGSEGV, Segmentation fault.
> 0xb6e81234 in memcpy () from /lib/libc.so.6
> //段错误，崩溃地址0xb6e81234属于/lib/libc.so.6，说明不是memcpy库本身Bug,是传给memcpy的参数非法
> //库函数执行的时候访问非法内存触发段错误
> ```
> 
> 接着执行`bt`，得到（bt（backtrace打印调用栈）是查看调用栈，确认崩溃路径的）
> 
> ```C
> #0  0xb6e81234 in memcpy ()
> #1  0x000108ac in copy_frame (
>         dst=0xb4001000,
>         src=0xb5000000,
>         len=650324)
>     at camera.c:86
> 
> #2  0x00010960 in process_frame ()
>     at camera.c:115
> 
> #3  0x00010a34 in main ()
>     at main.c:62
> ```
> 
> 这个时候才能定位错误
> 
> ```C
> main
> ↓
> process_frame
> ↓
> copy_frame
> ↓
> memcpy
> ↓
> SIGSEGV
> //说明就是这个路径下的memcpy出现了段错误
> ```
> 
> 依次看栈帧`frame 1`、`info args`得到（从系统库函数返回到自己的代码，检查memcpy传进来的参数，一步步追查看错误参数是怎么产生的）
> 
> ```C
> dst = 0xb4001000
> src = 0xb5000000
> len = 650324
> ```
> 
> 看`frame 2`、`info locals`得到
> 
> ```C
> width = 800
> height = 600
> bytes_per_pixel = 2
> bytesused = 960000
> 
> buffer_size = 614400
> 
> frame_buf = 0xb5000000
> copy_buf  = 0xb4001000
> ```
> 
> 这个时候根因就清楚了，实际上这一帧用了960000 byte，申请的buffer却只有614400 byte，发生内存越界。然后我就去看申请缓冲区的代码，发现是`display_buf = malloc(640 * 480 * 2);`后续修改为按照实际格式动态申请
> 
> ```C
> buffer_size = width * height * 2;
> display_buf = malloc(buffer_size);
> ```
> 
> 修复后再用gdb验证，发现程序正常运行。
> 
> 

# 互斥锁和自旋锁

> 都是为了解决同一个问题：多个线程同时访问共享资源的时候，保证同一时刻只有一个线程能够进入临界区。
> 
> 区别在于：获取不到锁的时候，线程怎么办？
> 
> 互斥锁：线程抢不到锁的时候就会被系统挂起，进入阻塞态，等锁被释放后再被唤醒
> 
> 自旋锁：线程抢不到锁的时候会一直轮询检查锁有没有被释放（等待期间会持续占用CPU）
> 
> 互斥锁唤醒后会有调度延迟，自旋锁的话锁释放后可立即获得
> 
> linux项目中用到互斥锁的位置：touchscreen\.c模块中的touchscreen\_read\(\)函数中加锁更新触摸坐标xy/is\_pressed，在touchscreen\_get\_event\(\)函数中判断触摸事件的时候加锁读取触摸坐标。gallery\.c模块中相册链表的保护，gallery\_load\_image\(\)扫描/Picture目录并插入链表时候加锁，访问链表节点时加锁。
> 
> Q：对死锁的理解，如何避免
> 
> A：两个线程互相拿着对方需要的资源，比如线程A拿到了资源1，等待资源2，但是线程B拿到了资源2，需要等待资源1。避免的方法的话，不要持有锁时间过长，加锁失败的话就释放已有的锁
> 
> Q：互斥锁的底层原理？
> 
> A：用一个共享变量来表示锁的状态，原子操作保证抢锁过程不会被打断，抢不到锁的线程进入阻塞等待，释放锁的时候唤醒等待者
> 
> 

# 进程和线程

> - 进程是资源分配单位。每个进程都有自己独立的虚拟地址空间，同一个进程里的多个线程共享代码段、数据段、堆、文件描述符等资源。\./imageplay就是建立了一个进程
> 
> - 线程是CPU调度执行单位，每个线程有自己独立的栈和寄存器上下文
> 
> Q：进程之间的通信方式？
> 
> A：父子进程之间可以使用管道pipe，还有就是消息队列、共享内存、信号量
> 
> Q：各自特点
> 
> A：进程之间的隔离性更好，但是创建和通信开销更大。线程共享资源，通信效率更高，但是要特别注意数据竞争、互斥锁和死锁问题
> 
> Q：进程间资源抢占如何解决
> 
> A：共享资源放入临界区，然后通过互斥或者同步机制保证同一时刻只有允许数量的执行流访问
> 
> Q：进程和线程退出
> 
> A：进程SIGKILL，线程pthread\_exit\(NULL\)
> 
> Q：进程切换和线程切换
> 
> A：进程切换还需要切换页表和地址空间
> 
> 

# 动态链接和静态链接

> 静态链接是发生在生成可执行文件的过程中。
> 
> 动态链接是程序运行时，系统把共享库加载进来。
> 
> 

# 系统内存分配

> 分为两部分，用户看到的虚拟内存以及操作系统实际管理的物理地址
> 
> 用户操作虚拟地址，通过页表映射到真实的物理地址上
> 
> Q：进行内存映射的好处
> 
> A：安全，不能随便访问其他进程的内存。方便管理，连续的虚拟地址不一定对应连续的物理地址
> 
> 

# 页表

> 常见的页大小：4KB
> 
> 虚拟地址被分为：虚拟页号\+页内偏移，最终实际物理地址=页框号\+页内偏移
> 
> 页表就是一张映射表：虚拟地址\-\>物理地址。同时还会记录一些权限与属性
> 
> 现代的操作系统都用的是多级页表，64位操作系统有4级索引\+页内偏移
> 
> MMU就是一个硬件，根据页表完成地址的翻译和权限检查
> 
> 三级页表：通常支持39位虚拟地址
> 
> 四级页表：通常支持48位虚拟地址
> 
> 页表项PTE（Page Table Entry）:页表项里包括页帧号\+权限与属性信息。页表项就是页表中的一条记录，里面写着“这个虚拟页映射到哪个物理页，以及允许怎么访问”。页表项占8字节
> 
> 多级页表查找像这样：
> 
> ```Plain Text
> L0 页表项：指向 L1 页表
> L1 页表项：指向 L2 页表
> L2 页表项：指向 L3 页表
> L3 页表项：指向真正物理页
> ```
> 
> 48 位虚拟地址拆法：
> 
> ```Plain Text
> VA[47:39]  第 0 级索引，9 bit
> VA[38:30]  第 1 级索引，9 bit
> VA[29:21]  第 2 级索引，9 bit
> VA[20:12]  第 3 级索引，9 bit
> VA[11:0]   页内偏移，12 bit
> ```
> 
> TTBR0 \-\> 用户空间页表
> 
> TTBR1 \-\> 内核空间页表
> 
> Q：MMU如何把虚拟地址转换为物理地址？
> 
> A：MMU就是一级一级查页表，最后把物理页基地址和页内偏移拼成真正的物理地址
> 
> Q：Linux内核如何将内核虚拟地址转换为页帧号PFN
> 
> A：PFN = 物理地址 \>\> PAGE\_SHIFT , 所以主要就是需要将内核虚拟地址转换为物理地址。很多内核地址都属于线性映射区，即内核虚拟地址 = 物理地址 \+ 页偏移
> 
> 

# ARM汇编各种跳转指令

> ```C
> 普通跳转：B
> 带返回地址跳转：BL
> 寄存器跳转：BR
> 带返回地址的寄存器跳转：BLR
> 函数返回：RET
> 条件跳转：B.cond
> 比较并跳转：CBZ / CBNZ
> 测试 bit 并跳转：TBZ / TBNZ
> ```
> 
> 

# 应用层调用open等函数，到达内核驱动的open函数的内核路径

> 应用层通过open\("/dev/xxx"\)通过系统调用进入内核，找到对应的设备节点inode，再根据对应的主次设备号找到内核注册的cdev以及file\_operations，最终调用驱动注册的\.open函数
> 
> 

# I2C驱动子系统

> I2C控制器驱动 i2c\_adapter
> 
> I2C从设备对象 i2c\_client
> 
> I2C设备驱动 i2c\_driver
> 
> 

# Linux调度策略

> 普通分时调度CFS：默认是SCHED\_OTHER，目标就是尽可能公平，每个进程按照权重获得CPU时间，权重大小和nice值有关，nice越小，优先级越高。
> 
> 实时调度：先进先出的实时策略SCHED\_FIFO，高优先级实时线程会一直运行，直到它主动阻塞或者是被更高优先级进程抢占
> 
> 

# 进程上下文

> 就是进程执行环境的全部信息
> 
> 主要包含：用户级上下文、寄存器上下文、内核级上下文
> 
> 

# 中断下半部

> Tasklet：属于软中断上下文，执行的过程仍属于原子上下文，不能睡眠，阻塞
> 
> workqueue：属于进程上下文，允许睡眠、阻塞
> 
> 

# 使用AI的心得

> AI是个提高效率的工具
> 
> - 学习新技术的时候，V4L2框架
> 
> - 代码分析，比较长的驱动代码，让AI把相关的函数调用链整理出来
> 
> 

# 团队分工

> 团队协作中如果遇到问题的时候，需要先把问题整理清楚再和其他成员沟通，不能简单说这里有问题，而是带着明确的日志和定位结论去寻求帮助。
> 
> 团队分工上，需要按模块拆分
> 
> 管理项目的时候需要确定各个模块之间的接口和数据格式，比如统一使用GloveData表示手套数据
> 
> 并且最后的任务结果需要闭环，不仅代码能跑，还要经得起验证以及文档留痕
> 
> 

# Makefile

> 目标、依赖、命令
> 
> Makefile文件就是用来描述哪些文件需要编译、怎么编译、目标文件之间有什么依赖关系
> 
> KERNELIDR:开发板的Linux内核源码目录，\.ko程序必须使用linux内核的kbuild构建系统，来保证生成的\.ko和目标板的linux内核匹配
> 
> obj\-m：xxx\.c编译成内核模块xxx\.ko
> 
> obj\-y：直接编译进内核zImage
> 
> 

# Shell

> 1. ps aux
> 
> ps\(proces status进程快照工具\)
> 
> a\(all termianals显示终端下的所有用户进程\)
> 
> u\(user\-oriented面向用户的详细输出\)
> 
> x\(no controlling terminal后台守护进程\)
> 
> ps是一次性快照
> 
> top是实时动态监控
> 
> ```C
> USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
> root     24500  0.0  0.2  12345  6780 ?        Ssl  10:00   0:02 ./test_app
> ```
> 
> 2. top 
> 
> \-p按CPU占用排序
> 
> \-M按照内存占用排序
> 
> top\(table of process进程表\)
> 
> 3. strace \./embedded\_camera
> 
> strace是看系统调用的耗时
> 
> ```C
> 10:21:01.123456 open("/dev/video1", O_RDWR) = 3 <0.000081>
> 10:21:01.123700 ioctl(3, VIDIOC_S_FMT, 0xbefff1a0) = 0 <0.002314>
> 10:21:01.126210 ioctl(3, VIDIOC_REQBUFS, 0xbefff180) = 0 <0.001102>
> 10:21:01.127500 ioctl(3, VIDIOC_QUERYBUF, 0xbefff140) = 0 <0.000423>
> 
> 10:21:01.128020 mmap(NULL, 960000, PROT_READ|PROT_WRITE,
>                     MAP_SHARED, 3, 0) = 0xb6f00000 <0.000091>
> 
> 10:21:01.128400 ioctl(3, VIDIOC_QBUF, 0xbefff100) = 0 <0.000312>
> 10:21:01.128800 ioctl(3, VIDIOC_QBUF, 0xbefff100) = 0 <0.000287>
> 
> 10:21:01.129200 ioctl(3, VIDIOC_STREAMON, [V4L2_BUF_TYPE_VIDEO_CAPTURE])
>                  = 0 <0.001430>
> 
> 10:21:01.130800 ioctl(3, VIDIOC_DQBUF, 0xbefff100)
>                  = 0 <0.032516>
> 
> 10:21:01.163500 write(1, "get frame success\n", 18)
>                  = 18 <0.000041>
> 
> 10:21:01.164000 ioctl(3, VIDIOC_QBUF, 0xbefff100)
>                  = 0 <0.000290>
> 
> 10:21:01.164500 ioctl(3, VIDIOC_DQBUF, 0xbefff100)
>                  = 0 <0.033104>
> ```
> 
> 4. dmesg
> 
> dmesg用来查看内核环形缓冲区里的日志
> 
> dmesg \| tail \-50只看最近几十行
> 
> dmesg \| grep \-i ov5640
> 
> 加载驱动后会看到
> 
> ```Plain Text
> ov5640 1-003c: OV5640 detected
> mx6s-csi 21c4000.csi: registered video device
> video1: registered as /dev/video1
> ```
> 
> 如果驱动 probe 失败，可能看到：
> 
> ```Plain Text
> ov5640 1-003c: failed to read chip id
> ov5640: probe failed with error -5
> ```
> 
> 如果 I2C 有问题，可能类似：
> 
> ```Plain Text
> i2c i2c-1: transfer timed out
> ov5640 1-003c: i2c read failed
> ```
> 
> 如果 Camera 流启动失败，可能出现：
> 
> ```Plain Text
> mx6s-csi: stream start failed
> mx6s-csi: timeout waiting for frame
> ```
> 
> 所以你调试时通常可以这样：
> 
> ```Plain Text
> insmod ov5640.ko
> dmesg | tail -30
> ```
> 
> 
> 
> 

# gcc编译器

> - \-I：头文件搜索路径
> 
> - \-L：库文件搜索路径
> 
> - \-l：链接某个库
> 
> - \-o：执行输出文件名
> 
> - \-c：只编译不链接
> 
> - \-D：定义宏
> 
> - \-g：开启调试信息
> 
> - \-O：优化等级
> 
> \-O0：没有优化，调试用
> 
> \-O2：最常用
> 
> 

# rcS和\.bashrc

> rcS：系统进入用户空间后的初始化（加载驱动模块，挂载/proc、/sys、/dev）
> 
> \.bashrc：shell登录后用户环境的配置（配置用户环境的，PATH、环境变量等等）
> 
> 

# Linux驱动

> 处于硬件和应用层之间
> 
> 一方面可以通过寄存器、总线等方式操作实际的硬件设备，另一方面可以把操作硬件的能力通过Linux统一接口提供给应用层
> 
> 

# 操作系统包含哪些组成单元

> 内核、系统调用接口、shell、应用程序、驱动程序
> 
> 

# 数据竞争

> 数据竞争就是多个线程并发访问同一个共享变量，并且其中至少有一个在写这个变量，而这些访问之间又没有做同步保护，这个时候程序的结果可能取决于线程具体的执行时序。
> 
> 比如两个线程同时执行count\+\+，这个操作底层不是一步完成的，而是读出count、加1、再写回内存，如果两个线程同时读取到相同旧值，再分别＋1后写回，就可能出现少加一次的情况。
> 
> 

# 如何定位问题

> 先判断问题属于那一层
> 
> ```C
> 应用层
> → 系统调用
> → 驱动
> → 硬件
> ```
> 
> - debug:确定卡在那个函数
> 
> - top:看cpu、内存占用
> 
> - strace:看是否阻塞在read/write/ioctl
> 
> - gdb:看崩溃、调用栈
> 
> - demsg:看驱动、内核报错
> 
> 

# 子进程崩溃是否会影响到父进程

> 通常不会，因为进程具有独立的地址空间。内核会通过`SIGCHLD`通知父进程，父进程可以通过wait获取退出原因。但是如果父子进程共享内存或者父进程业务依赖子进程，子进程崩溃可能造成间接影响
> 
> fork\(\)创建子进程，调用一次，返回两次。
> 
> 父进程返回值：子进程PID
> 
> 子进程返回值：0
> 
> 

# mmap

> mmap返回的是一段虚拟地址的起始地址
> 
> Q：mmap区域位于哪里，有没有大小限制
> 
> A：mmap区域位于进程的虚拟地址空间里，没有在固定的物理内存某个位置。32位进程理论虚拟地址空间大小为4GB，但还要分给内核，用户空间大小约为3GB。此外，mmap 1GB并不等于马上占用1GB物理内存，真正访问的时候会触发缺页异常，内核才会分配物理页
> 
> 

# 设备树属性

> coherent：这个设备的DMA和CPU Cache在硬件上是缓存一致的
> 
> Q：这个coherent性能好，为什么不默认打开
> 
> A：有的硬件不支持Cache一致性
> 
> 

# 运行可执行文件后，OS层级发生了什么

> 运行可执行文件后，shell使用fork创建子进程，再由子进程调用execve把当前子进程的用户态地址空间替换成新的ELF程序，接着内核解析ELF文件，根据文件头信息创建VMA和页表，设置stack、PC、SP，接着动态链接完程序后从\_start进入C运行库初始化，最后调用main函数
> 
> Q：fork原理
> 
> A：基于当前进程创建一个子进程，此外并不会立即复制整个进程内存，而是通过COW（copy on write）延迟复制
> 
> Q：fork\(\)之后子进程会修改\.text吗
> 
> A：不会修改，因为代码段是可读可执行不可写的
> 
> 

# linux软件完整构建链路

> ```C
> Source Code 源代码
> ↓
> configure / cmake / make
> ↓
> gcc / cross‑gcc 编译器
> ↓
> object 目标文件(*.o)
> ↓
> link 链接
> ↓
> executable可执行程序 / .so动态库
> ↓
> package 软件打包
> ↓
> DEB软件包格式（DEB就是Debian/Ubuntu家族）
> ↓
> repository 软件仓库
> ↓
> Linux distribution Linux发行版
> ```
> 
> 

# 工具链

> gcc：C/C\+\+编译器
> 
> ld：链接
> 
> ar：\.o打包成静态库\.a
> 
> 

# 1

