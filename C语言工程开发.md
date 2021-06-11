# 多模块编程

```c
gcc -o program main.c //用main.c生成可执行文件program
./program //运行program可执行文件
```

当出现多文件编译的时候，会先将每个模块编译成*.o目标代码文件，然后进行链接，最后将链接好的文件转化成可执行文件

```c
gcc -c -o set.o set.c //将set.c文件编译成一个set.o目标代码
gcc -o program main.o set.o others.o //将set.o others.o与main.o链接在一起并输出可执行文件program
```



# 预处理

为避免头文件重复声明的情况，可用预处理执行#ifndef xxx和 #endif，形成一个代码块，如果并未已定义xxx宏，那么在#ifndef xxx 和 #endif之间的内容是有效的，例如：

```c
#ifndef PROJECT_PATH_FILE_H_
#define PROJECT_PATH_FILE_H_
typedef enum Status {Success, Fail};
typedef struct {
    char *name;
    int age;
} People;
Status go_to_Jisuanke(People);
#endif
```

习惯上定义宏的名字形式为：工程名_路径名_文件名_H_



# Makefile

使用make命令可读取Makefile文件，并根据Makefile中的规则把源文件生成可执行文件

Makefile中包含了一系列如下的规则，注意每条规则的命令前，必须有一个制表符\t

```makefile
目标： 依赖1 依赖2 ...
	命令

array.o: array.c array.h
	gcc -c -o array.o array.c
```

表示生成的目标代码文件array.o，依赖于array.c array.h，当在命令行中执行make array.o时，根据此规则，如果array.o不存在或array.c与array.h至少一个比array.o更新，就会执行gcc -c -o array.o array.c

注意：Makefile里除了当前目录隐藏文件外的第一个目标会成为运行make不指定目标时的默认目标

```makefile
main: array.o main.o
	gcc -o main array.o main.o
	
main.o: main.c array.h
	gcc -c -o main.o main.c
	
array.o: array.c array.h
	gcc -c -o array.o array.c
```

在Makefile中有多条规则时，如果只希望生成其中一个，可以在make命令后加上需要生成的目标名称。例如可执行 make main.o、make array.o、make main，当执行make main时，make指令发现array.o和main.o不存在，就会根据以它们为目标的规则先生成它们

Makefile示例：

```makefile
#井号开头的行是一个注释
#设置C语言编译器
CC = gcc

# -g 增加调试信息
# -Wall 打开大部分警告信息
CFLAGS = -g -Wall

#整理一下main依赖那些目标文件
MAINOBJS = main.o array.o

#.PHONY用于声明一些伪目标
.PHONY: clean

main: $(MAINOBJS)
	$(CC) $(CFLAGS) -o main $(MAINOBJS)
	
array.o: array.c array.h
	$(CC) $(CFLAGS) -c -o array.o array.c
	
main.o: main.c array.h
	$(CC) $(CFLAGS) -c -o main.o main.c
	
clean:
	rm -f $(MAINOBJS) main
```

