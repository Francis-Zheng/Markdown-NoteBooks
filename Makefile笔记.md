<!-- TOC -->

- [1. g++ -MM xxxxx.c](#1-g--mm-xxxxxc)
- [2. 一、多文件编译的总体结构](#2-一多文件编译的总体结构)
- [3. 方法 1 （ 多文件编译——使用makefile ）](#3-方法-1--多文件编译使用makefile-)
- [4. 方法 2 （使用变量——改进1）](#4-方法-2-使用变量改进1)
- [5. 方法 3 （使用自动推导——改进2）](#5-方法-3-使用自动推导改进2)

<!-- /TOC -->


# 1. g++ -MM xxxxx.c
查看xxxxx.c文件的所有依赖

# 2. 一、多文件编译的总体结构

如下图所示， 本示例 共包含 float类型加法、加法头函数、int类型加法、main主函数、float类型减法、减法头函数、int类型减法.

文件目录：
add_float.c
add.h
add_int.c
main.c
sub_float.c
sub.h
sub_int.c

主函数:
```c
#include "add.h"
#include "sub.h"
#include <stdio.h>
int main()
{
        int x, y;
        float a, b;
        x=5;
        y=2;
        a=5.5;
        b=2.2;
        printf("%d + %d = %d/n", x, y, add_int(x, y));
        printf("%3.1f + %3.1f = %3.1f/n", a, b, add_float(a, b));
        printf("%d - %d = %d/n", x, y, sub_int(x, y));
        printf("%3.1f - %3.1f = %3.1f/n", a, b, sub_float(a, b));
        return 0;
}
```


加法头函数
```c
/* add.h */
#ifndef _ADD_H_
#define _ADD_H_
extern int add_int(int x, int y);
extern float add_float(float x, float y);
#endif
```

int类型加法
```c
/* add_int.c */
int add_int(int x, int y)
{
        return x+y;
}
```

float类型加法
```c
/* add_float.c */
float add_float(float x, float y)
{
        return x+y;
}
```

减法头函数

```c
/* sub.h */
#ifndef _SUB_H_
#define _SUB_H_
extern int sub_int(int x, int y);
extern float sub_float(float x, float y);
#endif
```

int类型减法
```c
/* sub_int.c */
int sub_int(int x, int y)
{
        return x-y;
}
```

float类型减法
```c
/* sub_float.c */
float sub_float(float x, float y)
{
        return x-y;
}
```


# 3. 方法 1 （ 多文件编译——使用makefile ）

此方法为了避免方法1的不足，利用Linux GNU make工程管理器，进行编译、管理源文件。


首先，了解一下make和makefile。 GNU make是一个工程管理器，专门负责管理、维护较多文件的处理，实现自动化编译。如果一个工程项目中，有成百上千个代码源文件，若其中一个或多个文件进过修改，make就需要能够自动识别更新了的代码，不需要像方法1一样逐个输入编译冗长的命令行，就可以完成最后的编译工作。make执行时，自动寻找makefile(Makefile)文件，然后执行编译工作。因此，我们需要自己编写makefile文件（Makefile与makefile都可以直接被make命令识别，下同。但Linux区分大小写）来管理、维护工程文件，提高实际项目的工作效率。


其次，需要注意Linux makefile(Makefile)文件的编写规范和方法：

1. 需要由make工具创建目标体target，即通常的目标文件或可执行文件

2. 声明并给出创建的目标体所依赖的文件（dependency-file）

3. 编写完成创建每个目标体时所需要执行的命令（command）

具体格式如下：
```cpp
target： dependency-file1 dependency-file2 dependency-file3 ...

command
```

- `target`：规划的目标。通常是程序中间体或最后所需要生成的文件名，如 *.o或obj可执行文件的名称。此外，target目标也可以是make执行动作的名称，如clean等

- `dependency-file`：规则的依赖。生成规则目标所需要的文件名列表，通常是一个目标依赖于一个或多个文件。

- `command`：规则的命令。make程序所执行的的动作，可以为shell命令或者在shell下执行的程序。一个规则可以有多条命令，每条命令占一行。 在此特别需要注意的是每条命令行开始必须以Tab字符缩进开始，Tab缩进字符会告诉make命令此行是一个命令行，make按照命令完成此行相应的动作。这是在书写makefile（Makefile）文件时最易忽视和犯错的地方，而且大多比较隐蔽。

命令实质上市对任何一个目标的依赖文件发生变化后重建目标的动作描述。一个目标可以没有依赖而只有动作，即只有命令，如`clean`。此目标只有命令，没有依赖，主要作用是用来删除make过程中产生的中间文件（*.o），做收尾清理工作。


最后，上面均是纸上谈兵，现在我们来看具体实例，以直观、具体、详尽的解释makefile文件的编写方法和规则。

说明：

`#`表示注释，其后的在编译预处理时，将被全部删除不执行

`gcc -c` 编译C语言源文件，编译生成目标文件 *.o

`gcc -o` 定义生成文件名称，可以为 *.o（目标文件）和 main（可执行文件）

`rm -f *.o main` 强制删去该目录下的所有*.o 目标文件和main可执行文件

评析： 方法2利用makefile文件，进行项目所有文件的编译管理，可保存、易修改，且编译执行效率高，大大减轻了每次编译的工作量


# 4. 方法 2 （使用变量——改进1）

在编写makefile文件时，各部分引用变量的格式规范

1. make变量引用不同于Linux Shell变量引用规则，而是需加括号，即 `$(Var)` 格式，无论 Var 是单字符变量名还是多字符变量名均可。

2. c在命令行中出现的Shell变量，引用Shell的 `$tmp` 格式，一般为执行命令过程中的临时变量，不属于makefile变量，而是Shell变量。

3. 对出现在命令行中的make变量，同样使用` $(Command) `格式来引用。

纸上得来终觉浅，绝知此事要躬行。输入vim makefile命令，在Shell 利用vim编辑器来编写makefile文件，具体写法如下：

```c
OBJ=main.o add_int.o add_float.o sub_int.o sub_float.o
make: $(OBJ)
        gcc -c main.c -o main.o
add_int.o: add_int.c add.h
        gcc -c add_int.c -o add_int.o
add_float.o: add_float.c add.h
        gcc -c add_float.c -o add_float.o
sub_int.o: sub_int.c sub.h
        gcc -c sub_int.c -o sub_int.o
sub_float.o: sub_float.c sub.h
        gcc -c sub_float.c -o sub_float.o
clean:
        rm -f $(OBJ) main
```

# 5. 方法 3 （使用自动推导——改进2）

编写makefile文件，让make命令自动推导。只要make看到了 `*.o` 文件，它就会自动把与之对应的 `*.c` 文件加到依赖文件中，并且`gcc -c *.c` 也会被推导出来，所以makefile就简化啦。 此外，我们使用 `$(Command)` 格式，来引用命令变量。具体做法如下

首先，在Shell输入 `vim makefile` ，利用VIM编辑makefile文件内容

```c
CC=gcc
OBJ=main.o add_int.o add_float.o sub_int.o sub_float.o
make: $(OBJ)
        $(CC) -o main $(OBJ)
main.o: add.h sub.h
add_int.o: add.h
add_float.o: add.h
sub_int.o: sub.h
sub_float.o: sub.h
PHONY: clean
clean:
        rm -f $(OBJ) main
```

# 方法5（使用自动变量（`$^ $< $@`）——改进3）

在编写makefile文件中，有三个非常有用的变量，即分别是 `$@ $^ $<` 其代表的具体意义如下：

`$@` : 目标文件

`$^` : 所有依赖文件

`$<` : 第一个依赖文件

具体使用方法如下例所示

首先，在Shell输入 vim makefile ，利用VIM编辑makefile文件内容

```c
CC=gcc
OBJ=main.o add_int.o add_float.o sub_int.o sub_float.o
make: $(OBJ)
        $(CC) -o $@ $^
main.o: main.c add.h sub.h
        $(CC) -c $<
add_int.o: add_int.c add.h
        $(CC) -c $<
add_float.o: add_float.c add.h
        $(CC) -c $<
sub_int.o: sub_int.c sub.h
        $(CC) -c $<
sub_float.o: sub_float.c sub.h
        $(CC) -c $<
PHONY: clean
clean:
        rm -f $(OBJ) main
```
评析： 方法5在makefile文件中，引入参数变量、命令变量和自动变量，此方法编译系统，高效但不太直观，特别是维护修改不便，高手可秀。





# 编写Makefile文件
**1）定义变量**
首先定义SOURCE，OBJS和TARGET变量，用于指代我们项目中的源文件、目标文件和可执行文件。 
**2) 设置编译参数**
`CC`：配置编译器为g++， 
`LIBS`：需要调用的链接库（`-l开头`，去掉lib和.so。例：对 libopencv_core.so链接库的调用要写作：-lopencv_core）， 
`LDFLAGS`：链接库的路径（`-L开头`）， 
`INCLUDE`：头文件的路径。 
**3）链接生成** 
此步骤生成可执行文件（ELF），链接需要用到目标文件，由下一步产生 
**4）编译** 
此步骤生成目标文件（.o） 
**5）清理** 
此步骤清理可执行文件和所有的目标文件


```c
#######################
# Makefile
#######################
# source object target
SOURCE := main.cpp func.cpp
OBJS   := main.o func.o
TARGET := main

# compile and lib parameter
CC      := g++
LIBS    :=
LDFLAGS := -L.
DEFINES :=
INCLUDE := -I.
CFLAGS  := 
CXXFLAGS:= 

# link
#$(TARGET):$(OBJS)
    $(CC) -o $@ $^

# compile
#$(OBJS):$(SOURCE)
    $(CC) -c main.cpp -o main.o
    $(CC) -c func.cpp -o func.o

# clean
clean:
    rm -fr *.o
    rm -fr $(TARGET)
```


上述Makefile是将编译和链接两个步骤分开写的，我们同样可以直接从源文件生成可执行文件，自动进行编译链接等工作。 
方法：将上述Makefile中的：
```c
# link
#$(TARGET):$(OBJS)
    $(CC) -o $@ $^

# compile
#$(OBJS):$(SOURCE)
    $(CC) -c main.cpp -o main.o
    $(CC) -c func.cpp -o func.o
```

修改为：
```c
all:
    $(CC) -o $(TARGET) $(SOURCE)
```

于是就简化成：

```c
#######################
# Makefile
#######################
# source object target
SOURCE := main.cpp func.cpp
OBJS   := main.o func.o
TARGET := main

# compile and lib parameter
CC      := g++
LIBS    :=
LDFLAGS := -L.
DEFINES :=
INCLUDE := -I.
CFLAGS  := 
CXXFLAGS:= 

all:
    $(CC) -o $(TARGET) $(SOURCE)

# clean
clean:
    rm -fr *.o
    rm -fr $(TARGET)
```






[Maxsolar's Linux Blog Makefile範例教學](http://maxubuntu.blogspot.com/2010/02/makefile.html)



























































