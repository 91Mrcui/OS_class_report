

# 操作系统技术报告Lab Week 02

## 实验内容
*  熟悉Linux下x86汇编语言编程环境.
*  验证实验 Blum’s Book: Sample programs in Chapter 04, 05 (Moving Data)。

## 实验环境
* windows系统下由VMware搭建的Linu虚拟机
* 操作系统：Ubuntu 
- 汇编器：gas (GNU Assembler) in AT&T mode
- 编译器：gcc
- 编辑器：vim

## 技术日志
### Chap04中的实验验证
 * 验证实验cpuid
 1. 首先我们得到保存为`cpuid.s`的汇编语言源代码程序，将使用GNU汇编器和GNU连接器构建成可执行程序 ，具体的命令如下：
 ```
 $ as -o cpuid.o cpuid.s
 $ ld -o cpuid cpuid.o
 ```
 2. 在连接器生成可执行文件后，就可以通过命令来运行该命名为 cpuid 的程序，运行命令和运行的结果如下：
 
 ```
 $ ./cpuid
 The processor Vendor ID is 'AuthenticAMD'
 ```
 
![屏幕截图 2022-02-23 130640](https://user-images.githubusercontent.com/87711064/156017877-d90bf8f4-d7f4-4ca6-b914-830667600c13.png)

 3. 使用编译器：进行汇编，按照书上的指引，使用gcc编译器对源代码进行汇编，由于gcc查找的是`main`标签，因此需要将源代码文件中的 `start`标签部分改为：

```
.globl main  
main:
``` 

修改后进行汇编和连接程序，其结果如下，结果一致：

```
gcc cpuid.s -m32 -o cpuid
```

![屏幕截图 2022-02-23 130640](https://user-images.githubusercontent.com/87711064/156017995-6290451a-f559-40fa-ac84-3d9d5bb8476d.png)

4. 调试程序：使用GDB来调试程序，首先，为调试汇编语言程序，需要使用` -gstabs`参数来重新汇编源代码，并通过命令gdb来打开程序进行调试：
```
as -gstabs -o cpuid.o cpuid.s
ld -o cpuid cpuid.o
gdb cpuid
```
调试过程和程序信息如下：

![cpu1](https://user-images.githubusercontent.com/87711064/156018037-aa6ce9b1-ea37-409c-b3fd-664e32ffe894.png)

其中，`run`指令可使运行程序，并在运行前后显示一些基本信息。为实现单步调试程序，可以通过`break`指令来设置断点，且设置断点的命令参数应是标签，设置断点后运行程序，程序会在断点处中断，并显示该行代码。
设置断点后，可以在断点处通过`next`或`step`指令进行单步调试，通过`cont`指令来使程序继续运行。

5. 调试程序的过程中查看数据：可以使用`info registers`命令来显示所有寄存器中的值、使用`print`命令来显示特定寄存器或者来自程序的变量的值、使用`x`来显示特定内存位置的内容，具体查询过程如下：
![re](https://user-images.githubusercontent.com/87711064/156018141-4c07fe0a-e97f-403c-bc30-7d0c6dcf3652.png)

![x](https://user-images.githubusercontent.com/87711064/156018159-e183d6ca-d06f-4392-afb7-47aa044e26ea.png)

除此之外，gdb还有许多常用指令，书上详细介绍的有：
```
break :设置断点
run :在gdb内运行启动程序
step或s或next或n: 单步调试程序
cont: 使程序继续运行
info registers: 显示全部寄存器的值
print: 显示某一寄存器或变量的值
print/d: 显示十进制的值
print/t: 显示二进制的值
print/x: 显示十六进制的值
x/nyz: 显示特定内存位置的值,n是要显示的字段数,y是输出格式,z是要显示字段的长度

```

更多gdb指令和详细内容可参考该博客：
[【GDB】gdb调试命令大全_我是Cj，一个正义之人-CSDN博客](https://blog.csdn.net/xiaorui51/article/details/50906750?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164559493616780271571153%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164559493616780271571153&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-50906750.first_rank_v2_pc_rank_v29&utm_term=gdb%E8%B0%83%E8%AF%95%E6%8C%87%E4%BB%A4&spm=1018.2226.3001.4187)

* 验证实验cpuid2
1. 在汇编语言程序中使用C库函数：为使用C库函数`printf`来显示cpu id，需要将字符串改为以空字符为结尾的`.asciz`命令定义的字符串、声明缓冲区等等，具体改动已经在cpuid2.s文件中体现。
2. 连接C库函数，在汇编语言程序中使用C库函数时，需要将C库文件连接到程序目标代码，在这里我们使用动态连接的方式进行连接。在连接过程中，我遇到了许多困难，如64位系统和32位的as汇编语言不兼容、linux上没有32位C语言库等等问题，在查询了许多资料后得以解决，详细内容下文将会进行讲解。
3. 连接的代码和程序运行的结果如下：
![1](https://user-images.githubusercontent.com/87711064/156018200-cd9b5d46-5385-40b7-a855-817a265de9e3.png)

### Chap05中的实验验证
* 验证实验sizetest1

输入程序执行命令和程序运行结果如下：

![sizetest1](https://user-images.githubusercontent.com/87711064/156018226-a76d5fcf-2d12-4b39-91d4-d2eb71a59c3f.png)

* 验证实验sizetest2

输入程序执行命令和程序运行结果如下：

![sizetest2](https://user-images.githubusercontent.com/87711064/156018248-d4502d13-6769-4b3d-857f-acf5292d7e05.png)

分析：可以看到虽然添加了10000字节的缓冲区，但可执行文件长度只增加了216字节，变化不是很明显。

* 验证实验sizetest3

![sizetest3](https://user-images.githubusercontent.com/87711064/156018274-135aa0be-4f8e-4e6f-865c-025f7abf7446.png)

分析：此时缓冲区的10000字节被添加到了程序中，使程序长度明显增大了许多。



#### 传送数据元素实验验证
* movtest1实验验证

用-gstabs参数汇编movtest1.s程序，连接并在调试器中运行，具体信息如下：
![move1](https://user-images.githubusercontent.com/87711064/156018326-72c5862a-e276-4f8e-839d-2007cf767566.png)

分析：可以看到，内存位置中存储的值被传送到了ECX寄存器，达到了预期的目的。

* movtest2实验验证

同样用-gstabs参数汇编movtest1.s程序，连接并在调试器中运行，具体信息如下：
![move2](https://user-images.githubusercontent.com/87711064/156018358-85d243ec-f68e-4c5d-9539-83c490ab8ad0.png)

分析：通过单步调试并查看`value`标签的内存位置，可以发现寄存器的值确实被存储在了内存位置中。

* movtest3实验验证

使用变址寻址方式，还调用了C库函数，因此需要使用动态连接器，程序运行如下：
![move3](https://user-images.githubusercontent.com/87711064/156018384-4e8b21ec-04b3-40ae-a648-0ce71c99c793.png)

分析：可以看到以变址寻址的方式遍历了数组并输出其元素。

* movtest4实验验证

同理执行程序，程序运行结果如下：

![move4](https://user-images.githubusercontent.com/87711064/156018397-b6a31a9b-91ef-4374-aafd-84d8cf761967.png)

分析：可以看到，程序演示了间接寻址模式。

* cmovtest实验验证

cmovx指令格式如下：
```
cmovx source, destination  //其中x是一个或者两个字母的代码，表示将触发传送操作的条件，取决于EFLAGS寄存器的当前值。
```

使用cmovx指令实现寻找最大整数，使用gdb进行调试，程序执行结果如下：

![cmov2](https://user-images.githubusercontent.com/87711064/156018480-ed8d4b96-92ba-4b16-b089-58dce2d01047.png)

分析：可以看到，通过该指令可以实现找到数组中的最大值的程序。


#### 交换数据元素实验验证

* swaptest实验验证

`bswap`函数可以使32位寄存器中的字节顺序发生翻转，测试程序执行过程如下：

![swaptest](https://user-images.githubusercontent.com/87711064/156018517-5b9ac9f6-ad26-46ce-8bbd-8eb22417e198.png)

分析：可以看到，通过`bswap`函数的调用，32位寄存器中的字节顺序发生了翻转。

* cmpxchgtest实验验证

`xchg`函数可以在两个寄存器之间或者寄存器和内存位置之间交换数值，而`cmpxchg`函数可以增加条件，将一个值和一个外部值进行比较，并根据结果选择交换两者的值，验证程运行结果如下：

![cmpxchg](https://user-images.githubusercontent.com/87711064/156018548-d8a16418-1ae7-491b-bf33-807b196d1692.png)

分析：可以看到，在执行`cmpxchg`指令前，寄存器ebx中的值为5，data中的值为10，执行`cmpxchg`指令后，data中的值变为5，寄存器ebx中的值被传送到data的内存位置。

* bubble实验验证

`bubble`为用汇编语言编写的冒泡函数，为更加清晰地进行了解，在gdb中进行调试，程序运行结果如下：

![bubble](https://user-images.githubusercontent.com/87711064/156018566-ab534497-57eb-46ba-b445-7041ed420056.png)

分析：可见，数组元素被排序成了正确的顺序。



## 遇到的问题

1. 64位系统和32位as语言不兼容的问题

问题描述：

由于as汇编语言是32位的，因此会和64位的系统产生不兼容的问题，所以会导致源代码文件编译不了。

解决方案：

在源代码文件前加上代码`.code32`，并用以下指令进行编译：
```
as --32 -o cpuid2.o cpuid2.s
```

2. 32位的C语言库不存在的问题

问题描述：

由于我的系统是64位的，因此在用install gcc命令时下载的C语言库是64位的，所以发生了32位C语言库缺失的问题，导致动态链接失效。

解决方案：

通过以下指令重新下载一个32位C语言库：
```
$ sudo apt-get install libc6-dev-i386
```
链接的代码改为：
```
$ ld -m elf_i386 -dynamic-linker /lib/ld-linux.so.2 -o test -lc test.o
```

