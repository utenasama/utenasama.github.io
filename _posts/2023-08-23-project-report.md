---
layout: post
title: a naive shell for linux
date: 2021-09-29 23:18 +0800
last_modified_at: 2022-10-01 01:08:25 +0800
tags: [report, code]
toc:  true
---

### 实验内容及结果分析

**1.** （5分）在操作系统课程实验中，要用make工具编译内核，要掌握make和makefile。makfile文件中的每一行是描述文件间依赖关系的make规则。本实验是关于makefile内容的，您不需要在计算机上进行编程运行，只要书面回答下面这些问题。
对于下面的makefile:
CC = gcc
OPTIONS = -O3 -o
OBJECTS = main.o stack.o misc.o
SOURCES = main.c stack.c misc.c
HEADERS - main.h stack.h misc.h
polish: main.c \$(OBJECTS)
	\$(CC) \$(OPTIONS) power \$(OBJECTS) -lm
main.o: main.c main.h misc.h
stack.o: stack.c stack.h misc.h
misc.o: misc.c misc.h

回答下列问题：

a. 所有宏定义的名字
`CC`, `OPTION`, `OBJECTS`, `SOURCES`, `HEADERS`
b. 所有⽬标⽂件名称
`polish`, `main.o`, `stack.o`, `misc.o`
c. 每个⽬标的依赖⽂件
- polish依赖文件：`main.c`, `main.o`, `stack.o`, `misc.o`
- main.o依赖文件：`main.c`, `main.h`, `misc.h`
- stack.o依赖文件：`stack.c`, `stack.h`, `misc.h`
- misc.o依赖文件：`misc.c`, `misc.h`
d. ⽣成每个⽬标⽂件所需执⾏的命令
`polish: \$(CC) \$(OPTIONS) power \$(OBJECTS) -lm
main.o: main.c main.h misc.h
stack.o: stack.c stack.h misc.h
misc.o: misc.c misc.h`
e. 画出makefile对应的依赖关系树
f. ⽣成main.o stack.o和misc.o时会执⾏哪些命令，为什么?
以下命令的作用是将源文件编译成目标文件。
main.o: `gcc -c main.c`
stack.o: `gcc -c stack.c`
misc.o: `gcc -c misc.c`


<br>

**2.** （5分）用编辑器创建main.c、compute.c、input.c、compute.h、input.h和main.h文件。下面是它们的内容。注意compute.h和input.h文件仅包含了compute和input函数的声明但没有定义。定义部分是在compute.c和input.c文件中。main.c包含的是两条显示给用户的提示信息。
（1）创建上述三个源文件和相应头文件，用gcc编译器，生成power可执行文件，并运行power程序。给出完成上述工作的步骤和程序运行结果。
{% highlight bash linenos %}
bill@t14:~/Documents/maketest2$ cat compute.h
/* compute函数的声明原形 */
double compute(double, double);
bill@t14:~/Documents/maketest2$ cat input.h
/* input函数的声明原形 */
double input(char *);
bill@t14:~/Documents/maketest2$ cat compute.c
#include <math.h>
#include <stdio.h>
#include "compute.h"
double compute(double x, double y)
{
        return (pow ((double)x, (double)y));
}
bill@t14:~/Documents/maketest2$ cat main.h
/* 声明用户提示 */
#define PROMPT1 "请输入x的值:"
#define PROMPT2 "请输入y的值:"
bill@t14:~/Documents/maketest2$ cat input.c
#include <stdio.h>
#include"input.h"
double input(char *s)
{
        float x;
        printf("%s", s);
        scanf("%f", &x);
        return (x);
}
bill@t14:~/Documents/maketest2$ cat main.c
#include <stdio.h>
#include "main.h"
#include "compute.h"
#include "input.h"

main()
{
        double x, y;
        printf("本程序从标准输入获取x和y的值并显示x的y次方.\n");
        x = input(PROMPT1);
        y = input(PROMPT2);
        printf("x的y次方是:%6.3f\n",compute(x,y));
}
bill@t14:~/Documents/maketest2$ gcc -c main.c input.c compute.c
main.c:6:1: warning: return type defaults to ‘int’ [-Wimplicit-int]
    6 | main()
      | ^~~~
bill@t14:~/Documents/maketest2$ gcc main.o input.o compute.o -o power -lm
bill@t14:~/Documents/maketest2$ ./power
本程序从标准输入获取x和y的值并显示x的y次方.
请输入x的值:2
请输入y的值:3
x的y次方是: 8.000
{% endhighlight %}

（2）创建Makefile文件，使用make命令，生成power可执行文件，并运行power程序。给出完成上述工作的步骤和程序运行结果。
Makefile文件：
```bash{.line-numbers}
CC = gcc  
CFLAGS = -Wall -Wextra -Wpedantic -O3  
LDFLAGS = -lm  
TARGET = main  
OBJECTS = main.o compute.o input.o  
  
$(TARGET): $(OBJECTS)  
	$(CC) $(CFLAGS) $(OBJECTS) -o $(TARGET) $(LDFLAGS)  
  
main.o: main.c main.h compute.h input.h  
	$(CC) $(CFLAGS) -c main.c  
  
compute.o: compute.c compute.h  
	$(CC) $(CFLAGS) -c compute.c  
  
input.o: input.c input.h  
	$(CC) $(CFLAGS) -c input.c  
  
clean:  
	rm -f $(OBJECTS) $(TARGET)  
```
运行结果：
```bash{.line-numbers}
root@t14:/home/bill/Documents/maketest2# make
gcc   -Wall -Wextra -Wpedantic -O3   main.o compute.o input.o   -o main   -lm
root@t14:/home/bill/Documents/maketest2# ./main
本程序从标准输入获取x和y的值并显示x的y次方.
请输入x的值:2
请输入y的值:3
x的y次方是: 8.000
```

<br>

**3.**（10分）编写shell 脚本，统计指定目录下的普通文件、子目录及可执行文件的数目，统计该目录下所有普通文件字节数总和，目录的路径名字由参数传入。（不能使用sed、awk等工具）
脚本代码如下：
```bash{.line-numbers}
#! /bin/bash
read -p "Enter the directory: " path
num=$(ls -R -l $path | grep '^-' | wc -l) # number of regular files
echo "The number of regular files: $num"
num=$(ls -R -l $path | grep '^d' | wc -l) # number of directory
echo "The number of directory: $num"
num=$(ls -R -Al $path | cut -d' ' -f1 | grep 'x' | wc -l)
echo "The number of executable file : $num"
total_size=0

get_total_size() {
for file in $1/*
do
	if [ -f $file ]
	then
		total_size=$(($total_size+$(wc -c $file | cut -d' ' -f1)))
	elif [ -d $file ]
	then
		get_total_size "$file"
	fi
done
}

get_total_size $path
echo "Total size is $total_size"
```

运行结果如下，通过ls -l命令检查，可知脚本是正确的。
```bash{.line-numbers}
root@t14:/home/bill/Documents/program# bash size_counting .
Enter the directory: .
The number of regular files: 17
The number of directory: 2
The number of executable file : 5
Total size is 55597
root@t14:/home/bill/Documents/program# ls -l
总计 56
-rwxrwxrwx 1 root root  1159  7月 10 21:05  dirsync
-rw-rw-r-- 1 bill bill 11032  7月 21 16:34  myvim_git.sh
-rw-rw-r-- 1 bill bill  4938  7月 22 11:47  myvim.sh
-rw-rw-r-- 1 bill bill   474  7月 10 17:07  palindrome
-rw-rw-r-x 1 bill bill   176  7月 21 11:46  shell_test
-rw-rw-r-- 1 bill bill     0  7月 21 10:36 "shell_test'"
-rw-rw-r-- 1 bill bill    63  7月 10 13:32  shell_test1
-rw-rw-r-- 1 bill bill   616  7月 10 17:07  size_counting
drwxrwxr-x 2 bill bill  4096  7月 21 09:58  testc
drwxr-xr-x 2 root root  4096  7月 10 17:09  testdir1
-rw-rw-r-- 1 bill bill    69  7月 22 11:44  test_vim
-rw-rw-r-- 1 bill bill     4  7月 14 12:55  test_vim1
-rw-rw-r-- 1 bill bill     0  7月 21 12:01  test_vim2
-rw-rw-r-- 1 bill bill     0  7月 21 12:04  test_vim3
```

<br>

**4.** （10分）编写shell 脚本，输入一个字符串，忽略（删除）非字母后，检测该字符串是否为回文(palindrome)。对于一个字符串，如果从前向后读和从后向前读都是同一个字符串，则称之为回文串。例如，单词“mom”，“dad”和“noon”都是回文串。（不能使用sed、awk、tr、rev等工具）
脚本代码如下：
```bash{.line-numbers}
#! /bin/bash

read -p "enter a string: " input_string
cleaned_string=""
reversed_string=""
for ((i = 0; i < ${#input_string}; i++)); do
    char="${input_string:i:1}"
    if [[ "$char" == [a-zA-Z] ]]; then
        cleaned_string+="${char,,}"
    fi
done
for ((i = ${#cleaned_string}-1; i >= 0; i--)); do
    reversed_string+="${cleaned_string:i:1}"
done
if [[ "$cleaned_string" == "$reversed_string" ]]; then
    echo "is a palindrome."
else
    echo "not a palindrome."
fi
```

脚本的基本思路是清理输入字符串中的非字母字符并转换为小写形式，然后比较清理后的字符串和其反转是否相等，以确定输入字符串是否为回文。

运行结果：
```bash{.line-numbers}
root@t14:/home/bill/Documents/program# bash palindrome
enter a string: adsfsda
is a palindrome.
root@t14:/home/bill/Documents/program# bash palindrome
enter a string: sadfsadf
not a palindrome.
root@t14:/home/bill/Documents/program# bash palindrome
enter a string: 0234-4320
is a palindrome.
```

**5.** （20分）编写一个实现文件备份和同步的shell脚本程序dirsync。程序的参数是两个需要备份同步的目录，如:
dirsync ~\dir1 ~\dir2 
dirsync程序实现两个目录内的所有文件和子目录（递归所有的子目录）内容保持一致（最新的）。程序基本功能如下。
- 备份功能：目标目录将使用来自源目录的最新文件，新文件和新子目录进行升级，源目录将保持不变。dirsync程序能够实现增量备份。
- 同步功能：两个方向上的旧文件都将被最新文件替换，新文件都将被双向复制。源目录被删除的文件和子目录，目标目录也要对应删除；反之也一样。
- 其它功能自行添加设计。
本题要求：不能使用现有的备份或同步程序，如：/usr/bin/rsync
提示：充分使用cp命令强大的功能。

备份功能：删除目标目录下的内容，然后将源目录下的所有内容复制到目标目录，测试结果如下。测试目标目录与源目录的区别在于名为b的目录不同，经过同步后，目标目录中的内容与源目录的内容相同。
```bash{.line-numbers}
root@t14:/home/bill/Documents/program# tree testa
testa
├── a
├── b
├── c
└── d

1 directory, 4 files
root@t14:/home/bill/Documents/program# tree testb
testb
├── a
├── b
│   └── b1
├── c
└── d

2 directories, 4 files
root@t14:/home/bill/Documents/program# bash dirsync ./testa ./testb
Backup or Synchronize (enter 1 or 2) ? :1
Backup succeed
root@t14:/home/bill/Documents/program# tree testa
testa
├── a
├── b
├── c
└── d

1 directory, 4 files
root@t14:/home/bill/Documents/program# tree testb
testb
├── a
├── b
├── c
└── d

1 directory, 4 files
```

同步功能：为了实现同步删除功能，我们需要将被删除的文件写入一个日志文件中，写入的格式为“[路径] [被删除文件名]”，比如`/home/bill/Documents/program/testa/c delete`。这样，使用同步删除功能时，首先遍历日志文件，查看需要同步删除的文件。然后利用命令`cp -urp $1/* $2/`和`cp -urp $2/* $1/`实现其他的同步功能。测试结果如下，首先将目录testa中的文件c和d删除，将目录testb中的文件a删除，文件删除记录于日志dirsync.txt中，经过同步删除后，目录中都只剩下a文件。

```bash{.line-numbers}
root@t14:/home/bill/Documents/program/testa# cat /var/log/dirsync.txt
/home/bill/Documents/program/testa/c delete
/home/bill/Documents/program/testa/d delete
/home/bill/Documents/program/testb/a delete
root@t14:/home/bill/Documents/program/testa# cd ..
root@t14:/home/bill/Documents/program# tree testa
testa
├── a
└── b

1 directory, 2 files
root@t14:/home/bill/Documents/program# tree testb
testb
├── b
├── c
└── d

1 directory, 3 files
root@t14:/home/bill/Documents/program# bash dirsync ./testa ./testb
Backup or Synchronize (enter 1 or 2) ? :2
Synchronize succeed
root@t14:/home/bill/Documents/program# tree testa
testa
└── b

1 directory, 1 files
root@t14:/home/bill/Documents/program# tree testb
testb
└── b

1 directory, 1 files
```

脚本代码如下：
```bash{.line-numbers}
#! /bin/bash

log_file=/var/log/dirsync.txt # 日志文件

del_file()	# 删除文件
{
rm -r "$1$3" 2>/dev/null
rm -r "$2$3" 2>/dev/null
}

sync_del()	# 同步删除
{
touch /tmp/dirsync.txt	# 创建临时文件
tmp_file=/tmp/dirsync.txt	# 临时文件
tac $log_file | while read line	# 逆序读取日志文件
do
	filename=$(cut -d' ' f1 <<< $line)	# filename = home/bill/dir/file
	sub_filename=${filename##$1} 	# filename = home/bill/dir/file $1 = home/bill/dir 
	sub_filename=${fileneme##$2}	# sub_filename = file
	if [ "$filename" != "$sub_filename" ]; then	# 如果文件名不等于子文件名
		grep "^$sub_filename" $tmp_file	# 在临时文件中查找子文件名
		if [ $? == 1 ]; then	# 如果没有找到
			op=$(cut -d' ' -f2 <<< $line)	
			if [ "$op" == "delete" ]; then	# 如果操作是删除
				del_file $1 $2 $sub_filename	# 删除文件
			fi
		fi
	fi
done
rm $tmp_file	# 删除临时文件
}

if [ $# -ne 2 ]; then	# 参数个数不等于2
	echo "Usage: $0 ~/dir1 ~/dir2"
	exit 1
fi

if [ ! -d "$1" ]; then	# 如果第一个参数不是目录
	echo "$1 is not directory"
	exit 1
fi

if [ ! -d "$2" ]; then	# 如果第二个参数不是目录
	echo "$2 is not directory"
	exit 1
fi

read -p "Backup or Synchronize (enter 1 or 2) ? :" mode	# 读取用户输入
if [ $mode == 1 ]; then	# 如果是备份
	rm -r $2/* 2>/dev/null	# 删除目录2下的所有文件
	cp -rp $1/* $2/	# 复制目录1下的所有文件到目录2
	if [ $? == 0 ]; then	# 如果复制成功
		echo "Backup succeed"	# 输出备份成功
	fi
elif [ $mode == 2 ]; then	# 如果是同步
	sync_del $1 $2		# 同步删除
	cp -rup $1/* $2/	# 同步复制
	cp -rup $2/* $1/ 	
	echo "Synchronize succeed"	# 输出同步成功
else	# 如果是其他模式
	echo "wrong mode"	# 输出错误模式
	exit 1	
fi
```

<br>

**6.**（20分）使用bash，编写一个命令行编辑器程序，编辑器的功能参考vim/vi，实现vim部分功能即可。不能直接调用现成的编辑器。
要求：不能使用开源代码，自己编写所有的代码。鼓励使用图形界面。
本实验题要求提供以下文档：
a. 有功能描述文档。
b. 设计文档，包括设计思想、功能模块、数据结构、算法等。
c. 源程序。有详细的注释和良好编程风格。

#### a. 功能描述文档
**一个简化版的命令行文本编辑器**

此文档描述了一个简单的命令行文本编辑器的实现，它具有基本的文本编辑功能，如浏览、编辑、插入、删除、撤销等。以下是该编辑器的主要功能及其功能描述：

1. **变量定义和初始化：**

   - `top_line`：记录屏幕顶部显示的行号。
   - `select_line`：记录当前选中的行号。
   - `old_buffer`：存储上一版本的缓冲区内容，用于实现撤销功能。
   - `buffer`：存储当前文本内容的缓冲区。
   - `is_modified`：记录文本是否已被修改。
   - `opened_file`：记录当前打开的文件名。

2. **`Refresh()` 函数：**

   - 刷新屏幕并显示当前文本内容，包括文件名、行数、大小等信息。

3. **`Quit()` 函数：**

   - 如果文本已修改，询问用户是否保存更改，根据用户输入执行相应操作。

4. **`Up()` 和 `Down()` 函数：**

   - 上移和下移选中行，同时根据选中行的位置调整屏幕的显示位置。

5. **`Edit_Line()` 函数：**

   - 编辑选中的行，在选中行上显示光标，并读取用户输入的修改。

6. **`Insert_Line_downwards()` 和 `Insert_Line_upwards()` 函数：**

   - 在选中行的上方或下方插入一行，并刷新屏幕以显示更改。

7. **`Del_Line()` 函数：**

   - 删除选中行，并根据需要刷新屏幕以显示更改。

8. **`Undo()` 函数：**

   - 恢复到上一版本的文本内容，实现基本的撤销操作。

9. **`Key_Map()` 函数：**

   - 根据输入的按键映射执行相应的操作，如上移、下移、退出、编辑、撤销、插入、删除等。

10. **`main()` 函数：**

    - 初始化编辑器，读取命令行参数并刷新屏幕，打开指定文件。
    - 进入主循环，刷新屏幕，监听用户输入，根据输入执行相应的操作，如键盘映射中定义的操作。

编辑器基于命令行界面，通过字符终端进行操作。用户可以使用键盘输入命令来浏览、编辑和保存文件内容。不同的键盘输入触发不同的编辑操作，例如上移、下移、编辑、插入、删除、撤销等。编辑器在每次操作后会刷新屏幕以反映最新的文本内容和选择状态。用户可以通过退出命令来结束编辑会话，同时可以选择是否保存对文本的修改。

此编辑器仅仅是一个简化版的文本编辑器，不支持所有复杂的编辑操作和高级功能，但它已经提供了完备的编辑能力以及对文本文件的查看和修改功能，已经支持用户对文本进行编辑操作。

#### b. 设计文档，包括设计思想、功能模块、数据结构、算法等

该命令行文本编辑器旨在提供一个基本的文本编辑环境，允许用户在终端中浏览、编辑和保存文本文件。该简易编辑器myvim设计基于以下思想：

1. **用户友好性：** 界面简洁，操作直观，用户可以轻松使用myvim编辑器进行基本文本编辑操作。

2. **键盘驱动：** 编辑器使用键盘输入来触发不同的编辑操作，通过键盘快捷键来实现上移、下移、编辑、插入、删除、撤销等功能。

3. **实时反馈：** 编辑操作后，屏幕会即时刷新以显示最新的文本内容和编辑状态，提供良好的用户体验。

**功能模块：**

1. **屏幕刷新模块 (`Refresh()`)：**
   - 获取文件大小和行数，显示在屏幕上方。
   - 根据当前选中行号和屏幕顶部行号，刷新屏幕中显示的文本内容。
   - 不同颜色显示选中行和其他行，提高可读性。

2. **退出和保存模块 (`Quit()`)：**
   - 如果文件已修改，提示用户保存更改。
   - 根据用户输入进行保存、不保存或取消操作。

3. **文本导航模块 (`Up()` 和 `Down()`)：**
   - 处理上移和下移操作，调整选中行号和屏幕顶部行号，以保持选中行在屏幕可见区域内。

4. **行编辑模块 (`Edit_Line()`)：**
   - 在选中行位置显示光标。
   - 读取用户输入的编辑内容，保存到缓冲区中。

5. **行插入和删除模块 (`Insert_Line_downwards()` 和 `Insert_Line_upwards()`；`Del_Line()`)：**
   - 插入和删除行，同时调整选中行号。
   - 更新缓冲区内容，修改状态为已修改。

6. **撤销模块 (`Undo()`)：**
   - 恢复到上一版本的缓冲区内容，实现基本的撤销功能。

7. **键盘映射模块 (`Key_Map()`)：**
   - 根据输入的键盘按键映射执行相应的功能模块，如上移、下移、退出、编辑、撤销等。

8. **主控制模块 (`main()`)：**
   - 初始化编辑器，获取命令行参数并打开指定文件。
   - 进入主循环，不断刷新屏幕，监听用户输入，根据输入执行相应的操作。

**数据结构：**

- `buffer`：用于存储文本内容的数组，每个元素表示一行文本。
- `old_buffer`：存储上一版本的文本内容，用于实现撤销功能。
- `top_line`：记录屏幕顶部显示的行号，用于控制屏幕滚动。
- `select_line`：记录当前选中的行号，用于定位光标和编辑操作。
- `is_modified`：记录文本是否已被修改的布尔值。
- `opened_file`：记录当前打开的文件名。

**算法：**

- 屏幕刷新算法：根据当前 `top_line` 和 `select_line` 以及屏幕大小，循环遍历缓冲区，逐行显示文本内容，并根据选中行号高亮显示。

- 上移和下移算法：根据用户输入的上移和下移命令，调整 `select_line` 和 `top_line`，以确保选中行在屏幕可见区域内。

- 编辑操作算法：在 `Edit_Line()` 函数中，根据用户输入在选中行的位置显示光标，并读取用户输入的编辑内容，将其保存到缓冲区中。

- 插入和删除行算法：在 `Insert_Line_downwards()`、`Insert_Line_upwards()` 和 `Del_Line()` 函数中，更新缓冲区，调整 `select_line` 和 `top_line`，并将修改状态设置为已修改。

- 撤销操作算法：在 `Undo()` 函数中，将 `old_buffer` 中的内容恢复到 `buffer` 中，以实现基本的撤销功能。

由于实现的是简易编辑器，设计时并未用到比较复杂的算法或者数据结构。

此设计文档概述了基于命令行的简化版文本编辑器的设计。通过模块化和分层设计，实现了基本的文本编辑功能，使用户能够在终端中浏览、编辑和保存文本文件。

#### c. 源程序
```bash{.line-numbers}
declare -i top_line=1   # 屏幕顶部行号
declare -i select_line=0   # 当前选中行号
declare -a old_buffer=()   # 旧缓冲区
declare -a buffer=()  # 缓冲区
declare is_modified=false   # 是否修改过
declare opened_file     # 打开的文件

function Refresh() {    # 刷新屏幕
    size=$(stat -c '%s' "$opened_file")  # 获取文件大小
    length=${#buffer[@]}                 # 获取文件行数
	(printf '\e[H\e[?25l%*s\r %s' "$COLUMNS"  "$select_line,${#buffer[select_line]}"
    printf ' "%s" %dL, %dB' $opened_file $length $size 
	printf '\n')	# 显示文件名，行数，大小
	local -i max_line=$top_line+$LINES-2    # 屏幕最大行数

    # 从顶部开始显示
	for ((i = top_line; i < max_line; i++)); do
		if ((i != select_line)) ; then  # 若不是选中行
			printf '\e[90m'            # 灰色
		fi
		if ((i > ${#buffer[@]})) ; then # 若超出文件行数
			printf '\e[K\e[35m~\e[m\n' # 紫色
		else 
			printf '\e[K%2s\e[m %s\n' "$i" "${buffer[i]}" # 显示行号和内容
		fi
	done
}

function Quit() {   # 退出
    if [ $is_modified == "true" ]; then   # 若修改过
        while true; do
            read -p "save or not [y/n]: " key_  # 询问是否保存
            if [ $key_ == "y" ]; then  # 若保存
                > "$opened_file"
                for line in "${buffer[@]}"; do
                    echo "$line" >> "$opened_file" # 将缓冲区的内容写入文件
                done
                echo -e "\033[?25h" # 显示光标
                exit 0
            elif [ $key_ == "n" ]; then # 若不保存
                echo -e "\033[?25h" # 显示光标
                exit 0  
            else    
                continue   # 重新输入
            fi
        done
    fi
    echo -e "\033[?25h" # 显示光标
    exit 0  # 退出
}

function Up() { # 上移
    if (( select_line > 1 )); then # 若不是第一行
        select_line=$(($select_line-1)) # 行号减一
    fi

    if (( select_line < top_line && top_line > 0 )); then # 若选中行号小于顶部行号且顶部行号大于0
        top_line=$(($top_line-1)) # 顶部行号减一
    fi
}

function Down() { # 下移
    if (( select_line < ${#buffer[@]} )); then # 若不是最后一行
        select_line=$(($select_line+1)) # 行号加一
    fi

    if (( select_line > top_line + $LINES -3 )); then # 超出屏幕能够显示的最大行数
        top_line=$(($top_line+1)) # 顶部行号加一
    fi
}


function Edit_Line() { # 编辑行
    if (( select_line == 0 )); then # 若选中行号为0
        return
    fi

    ifs_res=$IFS # 保存IFS
    IFS=$' ' # 设置IFS为换行符

    local -i cursor_pos=$select_line-$top_line+2 # 光标位置
    printf '\e[?25h\e[%sH' "$cursor_pos" # 显示光标

    read -rei "${buffer[select_line]}" -p "$(printf '%2s ' "$select_line")" mod_str # 读取输入
    if [ $# -eq 0 ]; then   # 若输入为空
        old_buffer=("" "${buffer[@]:1}"); unset old_buffer[0] # 将缓冲区的内容保存到old_buffer中
    fi
    buffer[select_line]=$mod_str # 将输入的内容保存到缓冲区中
    is_modified=true # 修改过

    IFS=$ifs_res # 恢复IFS
}

function Insert_Line_downwards() { # 向下插入行
    old_buffer=("" "${buffer[@]:1}"); unset old_buffer[0] # 将缓冲区的内容保存到old_buffer中
    buffer=("" "${buffer[@]:1:select_line}" "" "${buffer[@]:select_line+1}") # 将缓冲区的内容保存到buffer中
    unset buffer[0] # 删除buffer中的第一个元素
    is_modified=true # 修改过
    Down    # 下移
    Refresh # 刷新屏幕
    Edit_Line 1 # 编辑行
}

function Insert_Line_upwards() {    # 向上插入行
    old_buffer=("" "${buffer[@]:1}"); unset old_buffer[0]   # 将缓冲区的内容保存到old_buffer中
    buffer=("" "${buffer[@]:1:select_line-1}" "" "${buffer[@]:select_line}"); unset buffer[0] # 将缓冲区的内容保存到buffer中
    is_modified=true    # 修改过
    Refresh # 刷新屏幕
    Edit_Line 1 # 编辑行
}

function Del_Line() {   # 删除行
    old_buffer=("" "${buffer[@]:1}"); unset old_buffer[0]   # 将缓冲区的内容保存到old_buffer中
    buffer=("" "${buffer[@]:1:select_line-1}" "${buffer[@]:select_line+1}"); unset buffer[0]    # 将缓冲区的内容保存到buffer中
    is_modified=true    # 修改过
    if (( select_line == ${#buffer[@]} + 1 )); then # 若选中行号为最后一行
        Up
    fi
}

function Undo() {   # 撤销
    buffer=("" "${old_buffer[@]:1}"); unset buffer[0]   # 将old_buffer的内容保存到buffer中
    Refresh # 刷新屏幕
}

function Key_Map() {    # 键盘映射
    # if (( "$1" == "i" )); then
    #     cmd_mod=false
    # fi

    # if (( $cmd_mod == "true" )); then
        case "$1" in    # 根据输入的内容执行相应的操作
            up) Up;;    # 上移
            down) Down;;    # 下移
            q) Quit;;       # 退出
            e) Edit_Line;;  # 编辑行
            u) Undo;;       # 撤销
            o) Insert_Line_downwards;;  # 向下插入行
            O) Insert_Line_upwards;;    # 向上插入行
            d) Del_Line;;   # 删除行
        esac    
    # fi
}


function main() {   # 主函数
    if [ ! $# -eq 1 ]; then         # 文件数量不能为空，也不能超过一个
        echo "Usage: myvim [file]"
        exit 1
    # elif [ $# -gt 1 ]; then
    #     echo "Usage: myvim file"
    #     exit 1
    else 
        Refresh                     # 刷新屏幕
        opened_file="$1"            # 获取文件对象
        if [ ! -f "$1" ]; then
            touch "$1"              # 若不存在该文件，创建一个新文件
        fi
        buffer=()
        mapfile -t -O 1 buffer <"$opened_file"      # 将文件的每一行映射到缓冲区中，数组从下标为1的元素开始
        old_buffer=("" "${buffer[@]:1}"); unset old_buffer[0]   # 同样将数据储存在old_buffer中
        ((select_line++))        # 选中行号加一
        local -a temp_input=()   # 临时输入
        # local -i index=1

        while Refresh; do     # 刷新屏幕
            if read -rsN 1 -t 0.2 temp_input[0]; then   # 读取输入
                local -i index=1    # 输入的下标
                while read -rsN 1 -t 0.02 temp_input[$index]; do    # 读取输入
                    index=$(($index+1)) # 下标加一
                done

                local keyarg="" # 输入的内容
                if [[ ${#temp_input[*]} != 1 && ${temp_input[2]} == "A" ]]; then    # 若输入的内容为上
                    keyarg="up" # 上移
                elif [[ ${#temp_input[*]} != 1 && ${temp_input[2]} == "B" ]]; then  # 若输入的内容为下
                    keyarg="down"   # 下移
                else    # 若输入的内容不是上或下
                    keyarg=${temp_input[0]} 
                fi

                Key_Map "$keyarg"   # 键盘映射

                temp_input=()   # 清空输入
                # index=1
            fi
        done
    fi
}

main "$@"   # 执行主函数
```

**7.** （20分）使用任何一种程序设计语言实现一个shell 程序的基本功能。
shell 或者命令行解释器是操作系统中最基本的用户接口。写一个简单的shell 程序——myshell，它具有以下属性：
(一)	 这个shell 程序必须支持以下内部命令：bg、cd 、clr、dir、echo 、exec 、exit 、fg 、help、jobs 、pwd 、set 、test 、time 、umask。部分命令解释如下：
1)	cd <directory>  ——把当前默认目录改变为<directory>。如果没有<directory>参数，则显示当前目录。如该目录不存在，会出现合适的错误信息。这个命令也可以改变PWD 环境变量。
2)	pwd ——显示当前目录。
3)	time ——显示当前时间
4)	clr  ——清屏。
5)	dir <directory>  ——列出目录<directory>的内容。
6)	set  ——列出所有的环境变量。
7)	echo <comment>  ——在屏幕上显示<comment>并换行（多个空格和制表符可能被缩减为一个空格）。
8)	help ——显示用户手册，并且使用more 命令过滤。
9)	exit  ——退出shell。
10)	shell 的环境变量应该包含shell=<pathname>/myshell，其中<pathname>/myshell 是可执行程序shell 的完整路径（不是你的目录下的路径，而是它执行程序的路径）。
(二)	 其他的命令行输入被解释为程序调用，shell 创建并执行这个程序，并作为自己的子进程。程序的执行的环境变量包含一下条目：
parent=<pathname>/myshell。
(三)	 shell 必须能够从文件中提取命令行输入，例如shell 使用以下命令行被调用：
myshell batchfile 
这个批处理文件应该包含一组命令集，当到达文件结尾时shell 退出。很明显，如果shell 被调用时没有使用参数，它会在屏幕上显示提示符请求用户输入。
(四)	 shell 必须支持I/O 重定向，stdin 和stdout，或者其中之一，例如命令行为：
programname arg1 arg2 < inputfile > outputfile 
使用arg1 和arg2 执行程序programname，输入文件流被替换为inputfile，输出文件流被替换为outputfile。
stdout 重定向应该支持以下内部命令：dir、environ、echo、help。
使用输出重定向时，如果重定向字符是>，则创建输出文件，如果存在则覆盖之；如果重定向字符为>>，也会创建输出文件，如果存在则添加到文件尾。
(五)	 shell 必须支持后台程序执行。如果在命令行后添加&字符，在加载完程序后需要立刻返回命令行提示符。
(六)	 必须支持管道（“|”）操作。
(七)	 命令行提示符必须包含当前路径。

提示：
你可以假定所有命令行参数（包括重定向字符<、>、>>和后台执行字符&）和其他命令行参数用空白空间分开，空白空间可以为一个或多个空格或制表符。

项目要求：
1)	设计一个简单的全新命令行shell，至少满足上面的要求并且在指定的Linux 平台上执行。拒绝使用已有的shell程序的任何环境及功能。严禁使用开源代码。
2)	写一个关于如何使用shell 的简单的用户手册，用户手册应该包含足够的细节以方便Linux初学者使用。例如：你应该解释I/O 重定向、管道、程序环境和后台程序执行。
3)	源代码必须有很详细的注释，并且有很好的组织结构以方便别人阅读和维护；否则会扣除客观的分数。结构和注释好的程序更加易于理解，并且可以保证批改你作业的人不用很费劲地去读你的代码。

实验报告内容包括：
源代码文件、必要设计文档和用户手册，所有提交的文件以文本形式复制到报告内；还有myshell上测试各种命令的运行结果截图。

### myshell用户手册
**1. 编译与执行**
编译方法：终端输入命令g++ -o myshell myshell.cpp
运行可执行程序：./myshell



**2. I/O 重定向**
实现输入输出重定向需要将标准输入或标准输出替换为相应文件的输入或输出。通过使用 close() 函数关闭原有的标准输入/输出，然后使用 open() 函数来打开重定向的文件。检测是否需要进行重定向也是相对简单的，只需在命令分析阶段，检测是否存在 "> "、"< "、" >> " 等符号，一旦识别出这些符号，就可以为该命令添加相应操作的标记。

输入重定向示例：

```c
close(0);
open(InPath, O_RDONLY);
```

输出重定向示例（覆盖）：
```c
close(1);
open(OutPath, O_WRONLY | O_CREAT | O_TRUNC, 0666);
```

输出重定向示例（追加）：
```c
close(1);
open(OutPath, O_RDWR | O_CREAT | O_APPEND, 0666);
```

这种方法简洁而有效地实现了I/O重定向，允许将标准输入和输出与指定文件关联，从而实现对命令执行环境的灵活控制。在命令解析过程中，对特定的重定向符号进行识别，能够在运行时对输入输出进行合适的调整。

**3. 管道操作**
实现多级管道的一个便捷而有效的方法是利用一个自行创建的共享文件来存储中间结果。此方法中，最初 `fork` 的父进程用于创建和读写共享的中间文件。该父进程通过不断递归地调用 `fork` 来创建和回收新的进程，从而形成一个级联的进程链。

例如，首先调用 `fork` 创建一个执行 `CMD1` 的进程，该进程将其输出写入共享中间文件。然后，在回收进程1后，再次调用 `fork` 创建一个新的进程，用于执行 `CMD2`，并从共享文件中读取输入，将结果输出到共享文件中。整个过程通过共享文件进行简洁的协同完成。

通过这种方法，我们充分利用了 `fork` 进程的能力，使得每个进程可以独立地执行特定的命令，同时将中间结果存储在共享文件中。这种逐级调用 `fork` 并利用共享文件的方式，构建了一个有效的多级管道系统，简化了管道间数据传递的管理和控制。

**4. 程序环境**
在本次实验中，选择使用C/C++编程语言。该语言带来了诸多优势，其中环境变量管理方面更是显著的一大优势。C/C++语言允许我们通过调用系统库函数来操作Linux系统中的环境变量，这些变量在程序中具有全局范围，可以在不同的程序部分以及多个并发进程之间共享和修改。

环境变量是一种在操作系统中全局可见的键-值对，用于存储与系统操作和程序行为相关的信息。在C/C++中，我们可以使用`getenv()`函数来获取当前环境变量的值。这为我们提供了获取系统信息、配置选项和运行时参数的能力。例如，我们可以使用`getenv("PATH")`来获取系统的路径设置，从而更方便地定位可执行文件。此外，`setenv()`函数也为我们提供了在程序内部修改环境变量的方式，从而调整程序的行为或与其他程序进行交互。

环境变量在系统和程序之间起到了桥梁的作用，为开发者提供了方便的接口来控制和配置程序的行为。在Shell编程、系统管理以及开发各类应用时，充分利用环境变量的管理能力是至关重要的。此外，C/C++语言也提供了其他有用的系统调用和库函数，例如`getpid()`获取当前进程ID、`fork()`创建子进程等，这些功能进一步丰富了我们在程序环境中的操作和控制能力。

此外，C语言的信号处理函数也为程序的开发和管理带来了极大的方便。C语言提供了丰富的信号处理相关函数，例如signal()函数用于注册信号处理函数，kill()函数用于发送信号，以及一些处理特定信号的函数如SIGINT用于处理中断信号（例如Ctrl+C），SIGCHLD用于处理子进程状态改变信号等。这些信号处理函数使得我们能够更加精细地控制程序的响应和行为，从而实现更加健壮的应用程序。

通过C/C++语言进行实验的选择为我们提供了灵活且高效的环境变量管理能力，使得我们能够更加便捷地与系统交互，控制程序行为，以及构建复杂的系统应用。

**5. 后台程序执行**
在实现后台程序的执行过程中，关键的一环是维护进程表。进程表不仅需要在整个程序的生命周期中可被访问，而且需要被程序的所有子进程所共享和修改。仅仅将进程表设置为全局变量是不够的，因为这样只会被子进程继承，子进程的修改并不能影响到父进程，实际上相当于未对进程表进行任何更新。为解决这一问题，在这个实验中采用了共享内存的机制，通过一系列的`shmget`函数来申请和维护一个共享内存区域（存放jobs数组，后台进程总数和总进程数），将进程表存储在这个共享内存中，从而实现了进程表的共享与维护。

拥有了进程表之后，在命令分析阶段，当读取到"&"符号时，将当前进程标记为后台执行。在使用`fork`和`exec`的框架执行这个进程时，主进程不再使用`waitpid`的阻塞主进程选项，而是采用`waitpid`的`WNOHANG`选项，这使得主进程不会阻塞等待子进程。然而，当前父进程退出后，子进程可能仍在运行，因此我们需要知道何时后台的子进程结束，以便更新进程表，同时避免产生僵尸进程。在这里，我利用了子进程结束时会发出`SIGCHLD`信号的特性，通过捕获`SIGCHLD`信号和后台运行的标记，我成功实现了后台程序的运行。

当然，后台程序不仅仅包括通过"&"符号放入后台执行的进程，还包括使用`CTRL+Z`挂起在后台的进程。我们可以识别`SIGTSTP`信号，通过使用`kill`命令达到暂停的目的。在进程被暂停后，可以通过两种方式将其重新激活：一种是使用`bg`命令将其在后台继续执行，另一种是使用`fg`命令将其调回前台执行。这两种命令都依赖于`kill`命令向被挂起的进程发送`SIGCONT`信号，从而使其继续执行。唯一的区别在于，使用`fg`命令还会让主进程通过`waitpid`命令阻塞，等待该进程的完成，而`bg`命令则不需要阻塞，直接在后台完成即可。

### myshell命令手册
- **命令：bg**
    将一个或多个后台暂停的进程切换到后台运行。

    用法：
    bg %[JOB_ID]: 将指定的后台进程切换到后台继续运行。
    bg: 将最近的一个后台进程切换到后台继续运行。

- **命令：fg**
    将一个后台暂停或运行的进程切换到前台运行。

    用法：
    fg %[JOB_ID]: 将指定的后台进程切换到前台继续运行。
    fg: 将最近的一个后台进程切换到前台继续运行。

- **命令：jobs**
    显示所有后台运行或挂起的进程的状态。
    用法：jobs


- **命令：cd**
    更改当前工作目录。

    用法：
    cd [目录路径]: 将当前工作目录切换到指定路径。
    cd: 显示当前工作目录路径。

- **命令：pwd**
    显示当前工作目录。

    用法：pwd

- **命令：clr**
    清空终端屏幕。
    用法：clr

- **命令：time**
    显示当前时间。
    用法：time

- **命令：umask**
    显示或设置文件创建时的权限掩码。
    用法：
    umask [掩码]: 设置文件创建时的权限掩码。
    umask: 显示当前的权限掩码。

- **命令：dir**
    列出当前目录下的文件和子目录。
    用法：
    dir [目录路径]: 列出指定目录下的文件和子目录。
    dir: 列出当前目录下的文件和子目录。

- **命令：set**
    显示所有的环境变量。
    用法：set


- **命令：echo**
    显示给定的参数。

    用法：
    echo [参数1] [参数2] ...

- **命令：exec**
    执行外部命令。

    用法：
    exec [命令] [参数1] [参数2] ...

- **命令：test**
    比较字符串或数字，判断字符串是否为空或非空。

    用法：

    test [字符串/数字1] [比较操作符] [字符串/数字2]: 比较两个字符串或数字。
    test [操作符] [字符串]: 判断字符串是否为空或非空。

- **命令：unset**
    从环境中删除一个或多个环境变量。

    用法：
    unset [环境变量1] [环境变量2] ...

### myshell总体设计文档

#### 主体框架
Shell程序通常按照以下循环流程进行操作：读入命令 -> 命令语法分析 -> 执行命令 -> 读入命令。在读入命令这一部分，我选择了使用C++的string类型来存储命令，这样可以方便后续的分割和语法分析处理。读入命令后，我们使用`strtok`函数将命令行按照空格和制表符进行分割。这样可以将命令行分割成多个独立的部分，便于后续的处理。

接下来进入语法分析阶段，这个阶段的主要任务是识别命令中的重定向、管道和后台执行等特殊符号，并生成相应的参数列表。例如，如果命令中包含重定向符号`>`或`<`，则需要将重定向的文件名作为参数与命令一起传递给执行函数。如果命令中包含管道符号`|`，则需要将管道前后的命令分别生成参数列表，并将它们连接起来以便后续的管道操作。如果命令以`&`结尾，则表示需要在后台执行，需要相应地调整执行方式。

最后，在执行命令阶段，根据语法分析得到的参数列表，调用相应的执行函数来执行命令。执行函数可以是系统提供的内置命令，也可以是外部程序或脚本。执行完毕后，程序会继续读取下一个命令，继续进行循环流程。

通过这样的循环流程，Shell程序能够不断接收用户输入的命令，并按照语法规则进行解析和执行，实现了一个简单的命令行解释器。在实际的Shell程序中，还可以添加更多功能和细节，例如环境变量的处理、命令历史记录、自动补全等，以提供更好的用户体验和功能支持。

#### 主要功能模块
**a. 读取模块**
```c++{.line-numbers}
// 进程表的作用在于处理多进程的情况，当用户输入命令时，父进程会创建子进程来执行命令，父进程需要记录子进程的pid，以便于管理
    Jobs_init();    // 初始化jobs列表，并注册信号处理函数

    if (argc != 1) {    // 文件处理命令
        fstream infile;
        infile.open(argv[1], ios::in);  // 打开文件
        if (!infile) {  // 打开失败
            fprintf(stderr, "open file failed\n");
            exit(1);    
        }
        string cmd; // 读取文件
        while (getline(infile, cmd)) {  // 逐行读取
            vector<Command> *cmds = new vector<Command>;    
            arg_parser(cmd, cmds);  // 解析命令
            run_commands(*cmds);    // 执行命令
            delete cmds;            // 释放内存
        }
        infile.close(); // 关闭文件
        exit(0);        // 退出程序
    }

    cout << "welcome to myshell" << endl;   // 欢迎语
    rootpath = getcwd(NULL, 0); // 获取当前路径
    setenv("SHELL", rootpath.c_str(), 1);   // 设置环境变量

    string cmd;
    cout << "root@myshell " << getcwd(NULL, 0) << "$ "; // 打印提示符
    getline(cin, cmd);  // 读取命令
    while (cmd != "exit") { // 退出命令
        vector<Command> *cmds = new vector<Command>;     
        arg_parser(cmd, cmds);  // 解析命令
        run_commands(*cmds);    // 执行命令
        cout << "root@myshell " << getcwd(NULL, 0) << "$ "; // 打印提示符
        getline(cin, cmd);  // 读取命令
        delete cmds;    // 释放内存
    }
    cout << "bye" << endl;  // 再见语
    return 0;
```
模块介绍：
1. 首先，通过调用`Jobs_init()`函数初始化进程表，该进程表用于处理多进程的情况。当用户输入命令时，父进程会创建子进程来执行命令，父进程需要记录子进程的PID以便于管理。

2. 接下来，通过判断`argc`的值，判断是否为文件处理命令。如果`argc`不等于1，表示有命令行参数，需要处理文件输入。程序会打开指定的文件，并逐行读取文件内容。

3. 在文件处理模式下，每读取一行命令，会进行以下操作：
   - 创建一个`vector<Command>`类型的指针`cmds`，用于存储解析后的命令。
   - 调用`arg_parser`函数，将读取的命令进行解析，得到一组命令及其参数，将其存储在`cmds`中。
   - 调用`run_commands`函数，执行解析后的命令。
   - 释放`cmds`所占用的内存。
   
4. 如果文件处理模式下的文件打开失败，会输出错误信息并退出程序。

5. 如果没有命令行参数，表示进入交互模式。程序会输出欢迎语，并获取当前路径作为根路径，并将其设置为环境变量`SHELL`的值。

6. 在交互模式下，程序会进入一个循环，读取用户输入的命令，直到用户输入"exit"为止。循环中的操作与文件处理模式类似：
   - 创建一个`vector<Command>`类型的指针`cmds`，用于存储解析后的命令。
   - 调用`arg_parser`函数，将用户输入的命令进行解析，得到一组命令及其参数，将其存储在`cmds`中。
   - 调用`run_commands`函数，执行解析后的命令。
   - 输出提示符，等待用户输入下一个命令。
   - 释放`cmds`所占用的内存。

7. 当用户输入"exit"命令时，程序输出再见语，并退出。

读取输入模块通过判断命令行参数来确定是文件处理模式还是交互模式。在文件处理模式下，逐行读取文件内容并执行相应的命令。在交互模式下，循环读取用户输入的命令并执行。无论是文件处理模式还是交互模式，都会将读取的命令进行解析，并调用相应的函数执行命令。

**b. 语法分析模块**
```c++{.line-numbers}
void arg_parser(std::string &str_in, vector<Command> *cmd) {    // 解析命令
    cmd->clear();   // 清空命令
    const char delimiters[] = " \n\t";  // 分隔符
    const char* char_str = str_in.c_str();  // 转换为char*
    char* token = strtok(const_cast<char*>(char_str), delimiters);  // 分割命令，按照空格、换行、制表符分割

    vector<string> tmp; // 临时存储命令
    while (token != NULL) {     
        // printf("%s\n", token);  
        tmp.push_back(token);   // 存储命令
        token = strtok(NULL, delimiters);   // 继续分割
    }  

    Command *item = new Command();  // 临时存储命令
    int rein_flag = 0, reout_flag = 0, reapp_flag = 0;  // 重定向标志
    for (int i = 0; i < tmp.size(); i++) {  // 遍历命令
        if (rein_flag) {    // 输入重定向
            rein_flag = 0;  // 重定向标志置0
            item->inPath = tmp[i];  // 设置输入重定向路径
        }
        else if (reout_flag) {  // 输出重定向
            reout_flag = 0;     // 重定向标志置0
            item->outPath = tmp[i]; // 设置输出重定向路径
        }
        else if (tmp[i] == "<") {   // 输入重定向
            item->reIn = true;      // 设置输入重定向标志
            rein_flag = 1;          // 设置重定向标志
        }
        else if (tmp[i] == ">") {   // 输出重定向
            item->reOut = true;     // 设置输出重定向标志
            reout_flag = 1;         // 设置重定向标志
        }
        else if (tmp[i] == ">>") {  // 追加重定向    
            item->reApp = true;     // 设置追加重定向标志
            reout_flag = true;      // 设置重定向标志
        }
        else if (tmp[i] == "&") {   // 后台运行
            item->is_BG = true;     // 设置后台运行标志
        }
        else if (tmp[i] == "|") {   // 管道
            item->pipOut = true;    // 设置管道输出标志
            cmd->push_back(*item);  // 存储命令
            item->cmd_clear();      // 清空命令
            item->pipIn = true;     // 设置管道输入标志
        }
        else {  // 普通命令
            item->content.push_back(tmp[i]);
        }
    }
    cmd->push_back(*item);  
    return; 
}
```
上述代码是一个用于解析命令的函数`arg_parser`的实现。下面是对该函数的介绍：

1. 首先，通过调用`cmd->clear()`清空传入的命令列表`cmd`，以便存储新的解析结果。

2. 定义了一个字符串`delimiters`作为分隔符，包括空格、换行和制表符。

3. 将输入的命令字符串`str_in`转换为`const char*`类型，并将其赋值给`char_str`。

4. 使用`strtok`函数按照分隔符将命令字符串进行分割，得到一个个命令片段（token）。

5. 使用一个临时的`vector<string>`类型变量`tmp`来存储解析后的命令片段。

6. 使用循环遍历所有的命令片段，进行以下操作：
   - 如果`rein_flag`为真，表示前一个命令片段是输入重定向符号`<`，将当前命令片段作为输入重定向路径，并将`rein_flag`置为0。
   - 如果`reout_flag`为真，表示前一个命令片段是输出重定向符号`>`，将当前命令片段作为输出重定向路径，并将`reout_flag`置为0。
   - 如果当前命令片段是输入重定向符号`<`，将`item`的`reIn`标志置为真，表示有输入重定向，将`rein_flag`置为1。
   - 如果当前命令片段是输出重定向符号`>`，将`item`的`reOut`标志置为真，表示有输出重定向，将`reout_flag`置为1。
   - 如果当前命令片段是追加重定向符号`>>`，将`item`的`reApp`标志置为真，表示有追加重定向，将`reout_flag`置为1。
   - 如果当前命令片段是后台运行符号`&`，将`item`的`is_BG`标志置为真，表示要在后台运行。
   - 如果当前命令片段是管道符号`|`，将`item`的`pipOut`标志置为真，表示有管道输出，将`item`存储到命令列表`cmd`中，并清空`item`的内容，然后将`item`的`pipIn`标志置为真，表示有管道输入。
   - 如果以上条件都不满足，说明当前命令片段是普通命令，将其添加到`item`的`content`中。

7. 将最后一个`item`添加到命令列表`cmd`中。

8. 函数返回。

`arg_parser`函数通过遍历命令字符串的每个片段，根据不同的符号和标志对命令进行解析，并将解析结果存储在命令列表`cmd`中的`Command`对象中。其中，命令的解析包括输入重定向、输出重定向、追加重定向、后台运行、管道等功能。解析后的命令列表可以用于后续的执行操作。

**c. 命令执行模块**
```c++{.line-numbers}
void run_commands(vector<Command> &cmds) {  // 执行命令
    if (is_buildin_command(cmds[0])) {  // 内建命令
        execute_buildin_command(cmds[0]);   // 执行内建命令
        return;
    }

    pid_t pid = fork(); // 创建子进程，子进程用于执行命令，父进程用于管理job
    if (pid < 0) {  // 创建失败
        fprintf(stderr, "create process failed");
        exit(1);
    }
    else if (pid == 0) { // 子进程，用于执行命令
        while (true) {   // 必须等待父进程将job添加到进程列表中
            usleep(1);
            if (find_job(getpid()) == -1) { // 父进程还未将job添加到进程列表中
                usleep(1);
            } else {
               break;
            }
        }
        // cout << "child process:" << getpid() << endl;
        if (!cmds[0].pipIn && !cmds[0].pipOut) { // 普通命令，无管道
            if (cmds[0].reIn) { // 输入重定向
                close(0);   // 关闭标准输入
                int Fd = open(cmds[0].inPath.c_str(), O_RDONLY);    // 打开文件
                check_file_error(Fd);   // 检查文件是否打开成功
            }
            if (cmds[0].reOut) {    // 输出重定向
                close(1);   // 关闭标准输出
                int Fd = open(cmds[0].outPath.c_str(), O_WRONLY | O_CREAT | O_TRUNC, 0666); // 打开文件
                check_file_error(Fd);   // 检查文件是否打开成功
            }
            if (cmds[0].reApp) {    // 追加重定向
                close(1);   // 关闭标准输出
                int Fd = open(cmds[0].outPath.c_str(), O_RDWR | O_CREAT | O_APPEND, 0666);  // 打开文件
                check_file_error(Fd);   // 检查文件是否打开成功
            }
            if (!execute_buildin_command(cmds[0])) {    // 执行内建命令
                char* argv[cmds[0].content.size() + 1]; // 执行普通命令
                for (int i = 0; i < cmds[0].content.size(); i++) {  // 将命令转换为char*
                    argv[i] = const_cast<char*>(cmds[0].content[i].c_str());
                }
                argv[cmds[0].content.size()] = NULL;    // 最后一个参数为NULL
                if (execvp(argv[0], argv) == -1) {  // 执行命令
                    fprintf(stderr, "execvp failed: %s\n", strerror(errno));
                    exit(1);
                }
            }
            close(0);   // 恢复标准输入
            close(1);   // 恢复标准输出
        }
        else { // 管道
            for (int i = 0; i < cmds.size() - 1; i++) {
                pid_t pid2 = fork();    // 递归地创建子进程
                if (pid2 < 0) {
                    fprintf(stderr, "create process failed");
                    exit(1);
                }
                else if (pid2 == 0) { // 子进程处理管道
                    if (i != 0) {   // 不是第一个命令
                        close(0);   // 关闭标准输入
                        int Fd = open(pip_file, O_RDONLY);  // 打开管道文件
                    }
                    if (cmds[i].reIn) { // 输入重定向
                        close(0);   // 关闭标准输入
                        int Fd = open(cmds[i].inPath.c_str(), O_RDONLY);    // 打开文件
                        check_file_error(Fd);   // 检查文件是否打开成功
                    }
                    if (cmds[i].reOut) {    // 输出重定向
                        close(1);           // 关闭标准输出
                        int Fd = open(cmds[i].outPath.c_str(), O_WRONLY | O_CREAT | O_TRUNC, 0666); // 打开文件
                        check_file_error(Fd);   // 检查文件是否打开成功
                    }
                    if (cmds[i].reApp) {    // 追加重定向
                        close(1);           // 关闭标准输出
                        int Fd = open(cmds[i].outPath.c_str(), O_RDWR | O_CREAT | O_APPEND, 0666);  // 打开文件
                        check_file_error(Fd);   // 检查文件是否打开成功
                    }
                    close(1);   // 关闭标准输出
                    remove(pip_file);   // 删除管道文件
                    int Fd = open(pip_file, O_WRONLY | O_CREAT | O_TRUNC, 0666);    // 创建管道文件
                    check_file_error(Fd);   // 检查文件是否打开成功
                    if (!execute_buildin_command(cmds[i])) {    // 执行内建命令
                        char* argv[cmds[i].content.size() + 1]; // 执行普通命令
                        for (int j = 0; j < cmds[i].content.size(); j++) {  // 将命令转换为char*
                            argv[j] = const_cast<char*>(cmds[i].content[j].c_str());    
                        }
                        argv[cmds[i].content.size()] = NULL;    // 最后一个参数为NULL
                        if (execvp(argv[0], argv) == -1) {      // 执行命令
                            fprintf(stderr, "execvp failed: %s\n", strerror(errno));
                            exit(1);
                        }
                    }
                }
                else {
                    waitpid(pid2, NULL, 0); // 等待子进程执行完毕
                }
            }
            // 处理最后一条命令
            close(0);  
            int Fd = open(pip_file, O_RDONLY);  // 打开管道文件
            check_file_error(Fd);               // 检查文件是否打开成功
            if (cmds[cmds.size() - 1].reOut) {  // 输出重定向
                close(1);
                int Fd = open(cmds[cmds.size() - 1].outPath.c_str(), O_WRONLY | O_CREAT | O_TRUNC, 0666);   // 打开文件
                check_file_error(Fd);
            }
            if (cmds[cmds.size() - 1].reApp) {
                close(1);
                int Fd = open(cmds[cmds.size() - 1].outPath.c_str(), O_RDWR | O_CREAT | O_APPEND, 0666);    // 打开文件
                check_file_error(Fd);
            }
            if (!execute_buildin_command(cmds[cmds.size() - 1])) {  // 执行内建命令
                char* argv[cmds[cmds.size() - 1].content.size() + 1];   // 执行普通命令
                for (int i = 0; i < cmds[cmds.size() - 1].content.size(); i++) {    // 将命令转换为char*
                    argv[i] = const_cast<char*>(cmds[cmds.size() - 1].content[i].c_str());
                }
                argv[cmds[cmds.size() - 1].content.size()] = NULL;
                if (execvp(argv[0], argv) == -1) {
                    fprintf(stderr, "execvp failed: %s\n", strerror(errno));    // 执行失败
                    exit(1);
                }
            }
            close(0);   
            close(1);
        }

    }
    else {  // 父进程，添加进程表
        jobs_info->total_jobs++;    // 进程数加1
        if (jobs_info->total_jobs == MAX_JOB) {   // 进程数超过最大值
            fprintf(stderr, "too many jobs\n");     
            exit(1);
        }

        jobs[jobs_info->total_jobs - 1].set_job(pid, cmds[0].content[0].c_str(), !cmds[cmds.size() - 1].is_BG, false, false, -1); // 添加进程
        setenv("PARENT", rootpath.c_str(), 1); // 设置环境变量
        if (cmds[cmds.size() - 1].is_BG) {  // 后台进程
            jobs[jobs_info->total_jobs - 1].bg_index = (++jobs_info->bg_jobs); // 设置bg_index
            printf("[%d] %d\n", jobs[jobs_info->total_jobs - 1].bg_index, jobs[jobs_info->total_jobs - 1].pid); // 打印后台进程信息
        }
        jobs[jobs_info->total_jobs - 1].is_running = true;  // 设置进程状态
        if (!cmds[cmds.size() - 1].is_BG) { // 前台进程
            waitpid(pid, NULL, WUNTRACED);  // 等待子进程执行完毕
            if (jobs[jobs_info->total_jobs - 1].is_running) {   // 子进程未被暂停
                delete_job(pid);    // 删除job
            }
        }
        else {  // 后台进程
            waitpid(pid, NULL, WNOHANG);    // 无需等待子进程执行完毕
            // cout << "wait no hang" << endl;
        }
    }
}
```
命令执行模块介绍

命令执行模块负责执行用户输入的命令，并根据命令的特性进行相应的处理。该模块通过语法分析得到一个命令容器，然后根据容器中的命令特性进行判断和处理。

1.内建命令
首先，该模块会判断是否存在与重定向和管道操作无关的内建命令。这类命令可以直接在主进程中执行，而无需建立新的子进程。如果命令是内建命令，则会调用相应的内建命令函数进行执行，然后结束命令执行过程。

2.外部命令和管道命令
对于涉及重定向和管道操作的命令，该程序会创建一个新的子进程，在子进程中执行命令。如果命令不涉及管道操作，则被视为单个命令执行。在执行之前，会检查输入输出重定向的设置。内建命令使用 `Bulidin.h` 中的函数执行，而外部命令使用 `execvp` 函数执行。

如果命令涉及管道操作，意味着命令容器中包含多个命令，并且这些命令之间需要进行管道通信。在这种情况下，需要循环创建新的子进程，并在新的子进程中执行命令，直到命令容器中的所有命令都执行完毕。此时，原本的进程用于维护管道通信所需的共享文件 `temp.txt`。

3.父进程管理
在父进程中，主要负责建立当前命令的进程表信息。如果命令是后台执行的，则父进程不会等待子进程执行完毕，而是继续执行下一个命令。对于前台命令，父进程会被阻塞，直到子进程执行完毕。父进程还会维护进程表，并根据命令的执行情况进行相应的处理，如删除已完成的进程。

以上是命令执行模块的主要流程和功能。通过合理的进程管理和命令执行策略，该模块能够有效地执行用户输入的命令，并处理重定向和管道操作，以满足用户的需求。

**d. 信号处理模块**
```c++{.line-numbers}
void signal_init() {    
    signal(SIGTSTP, HANDLER_STOP);  // 处理ctrl z信号
    signal(SIGSTOP, HANDLER_STOP);  
    memset(&new_act, 0, sizeof(new_act));   
    new_act.sa_sigaction = HANDLER_SIGCHLD; // 处理子进程结束信号
    new_act.sa_flags = SA_RESTART | SA_SIGINFO; // 重新执行被信号中断的系统调用
    sigemptyset(&new_act.sa_mask);  // 清空信号集
    sigaction(SIGCHLD, &new_act, &old_act); // 注册信号处理函数
}


void HANDLER_STOP(int sign) {
    if (jobs[jobs_info->total_jobs - 1].pid != 0 && jobs[jobs_info->total_jobs - 1].is_running  && jobs[jobs_info->total_jobs - 1].is_FG) { // foreground process
        // cout << "test: ctrl z_in" << endl;
        kill(jobs[jobs_info->total_jobs - 1].pid, SIGSTOP); // 停止进程
        jobs[jobs_info->total_jobs - 1].is_FG = false;      // 标记为后台进程
        jobs[jobs_info->total_jobs - 1].is_running = false; // 标记为未运行
        jobs[jobs_info->total_jobs - 1].end_of_run = false; // 标记为未结束

        int job_index = jobs_info->total_jobs - 1;        // 获取当前job的索引
        jobs[job_index].bg_index = (++jobs_info->bg_jobs);  // 设置后台进程的索引
        cout << "[" << jobs[job_index].bg_index << "] " << jobs[job_index].pid << "    Stopped    " << jobs[job_index].job_name << endl; // 输出信息
    } else {
        cout << endl;   // 输出空行
    }
}


void HANDLER_SIGCHLD(int sign, siginfo_t* sign_info, void* context) { 
    // cout << "handler: SIGCHLD " << sign_info->si_pid << endl;
    if (BG_flag) { // background process
        BG_flag = 0;
        return;
    }
    int i = find_job(sign_info->si_pid);
    if (i != -1) { 
        //cout << "1" << endl;
        if (!jobs[i].is_FG && jobs[i].is_running) { // 后台进程结束
            //cout << "2: "<< i << " " << jobs[i].job_name << endl;
            //jobs[i].is_running = false;
            jobs[i].end_of_run = true;  // 标记为已结束
        }
        else if (!jobs[i].is_FG && !jobs[i].is_running) {   // 已经结束的后台进程
            //cout << "3" << endl;
        }
        else {
            //cout << "4" << endl;
            delete_job(jobs[i].pid);
        }
    } else {
        // cout << "error" << endl;
    }
}
```

信号处理模块主要负责对挂起当前进程的STGSTP信号与子进程结束的SIGCHLD信号做出响应。当接收到STGSTP信号时，该模块会暂停当前进程，并更新进程表的状态。对于子进程结束的SIGCHLD信号，该模块会更新进程表，以防止僵尸进程的产生。

在代码中，`signal_init()` 函数用于初始化信号处理设置。它注册了 `HANDLER_STOP` 函数来处理STGSTP信号，并注册了 `HANDLER_SIGCHLD` 函数来处理SIGCHLD信号。此外，通过设置 `new_act` 结构体的相关属性，可以实现在信号中断的系统调用重新执行。

`HANDLER_STOP` 函数用于处理STGSTP信号。当接收到该信号时，如果当前进程是前台进程且正在运行，则会将其暂停，并更新进程表的相关状态。如果当前进程是后台进程，则只输出一个空行。

`HANDLER_SIGCHLD` 函数用于处理SIGCHLD信号。当接收到该信号时，它会根据子进程的PID在进程表中查找对应的进程，并根据进程的状态进行相应的处理。如果是后台进程结束，则将其标记为已结束；如果是已经结束的后台进程，则不进行任何操作；如果是前台进程结束，则将其从进程表中删除。

通过信号处理模块，可以对挂起进程和子进程结束进行合适的响应，确保进程表的正确更新，并防止僵尸进程的产生。这样可以提高系统的稳定性和可靠性。

### 测试部分
**不带参数：**
```bash{.line-numbers}
bill@t14:~/Documents/myshell_test$ ./myshell 
welcome to myshell
bill@t14:~/Documents/myshell_test$
```

**带参数**
如参数为test.sh文件，则执行该文件中的命令：
```bash{.line-numbers}
bill@t14:~/Documents/myshell_test$ cat test.sh
ls -l
cd ..
ls -l
```

```bash{.line-numbers}
bill@t14:~/Documents/myshell_test$ ./myshell test.sh
总计 160
-rw-rw-r-- 1 bill bill  16084  8月 15 21:21 buildin_command.h
-rwxrwxr-x 1 bill bill 105784  8月 15 22:43 myshell
-rw-rw-r-- 1 bill bill  15199  8月 15 21:07 myshell.cpp
-rw-rw-r-- 1 bill bill   2714  8月 15 21:29 myshell.h
-rw-rw-r-- 1 bill bill   2978  8月 15 21:27 signal_handler.h
-rw-rw-r-- 1 bill bill  10539  8月 15 20:39 temp.txt
-rw-rw-r-- 1 bill bill     17  8月 15 20:43 test.sh
总计 80
-rw-rw-r-- 1 bill bill    16  7月  6 20:29 firscrip
-rw-rw-rw- 1 bill bill     0  7月  9 18:58 foobar
-rw-rw-r-- 1 bill bill    28  7月  9 19:07 foobar.path
drwxrwxr-x 2 bill bill  4096  8月  5 16:36 fork_test
drwxrwxr-x 2 bill bill  4096  8月 15 16:35 maketest2
drwxrwxr-x 3 bill bill  4096  7月 29 16:07 myshell_git
drwxrwxr-x 3 bill bill  4096  7月 29 16:38 myshell_git1
drwxrwxrwx 3 root root  4096  8月 15 22:43 myshell_test
drwxrwxr-x 6 bill bill  4096  8月 15 17:17 program
-rwxrwx-wx 2 root root  2318  7月  8 19:52 record.txt
-rwxrwx-wx 2 root root  2318  7月  8 19:52 record.txt.hard
-rw-rw-r-- 1 bill bill     0  7月  8 17:10 test1.html
-rw-rw-r-- 1 bill bill    43  7月  8 19:08 test1.txt
-rw-rw-r-- 1 bill bill     0  7月  8 17:11 test2.htm
-rw-rw-r-- 1 bill bill     0  7月  5 09:28 test2.txt
-rw-rw-r-- 1 bill bill     0  7月  8 17:11 test3.c
drwxrwxr-x 2 bill bill  4096  7月  9 19:33 testdir
-rwxrwxr-x 1 bill bill 16520  7月  6 16:19 testp
-rw-rw-r-- 1 bill bill    91  7月  6 16:22 testp1
-rw-rw-r-- 1 bill bill    95  7月  6 16:23 testp.cpp
-rw-rw-r-- 1 bill bill    13  7月  5 09:11 test.txt
```

**bg命令**
```bash{.line-numbers}
root@myshell /home/bill/Documents/myshell_test$ sleep 20
^Z
[1] 12490    Stopped    sleep
root@myshell /home/bill/Documents/myshell_test$ bg
[1] sleep
root@myshell /home/bill/Documents/myshell_test$ jobs
[1]+  12490  sleep Running
root@myshell /home/bill/Documents/myshell_test$ jobs
[1]+  12490  sleep Running
root@myshell /home/bill/Documents/myshell_test$ jobs
[1]+  12490  sleep Running
root@myshell /home/bill/Documents/myshell_test$ jobs
[1]+  12490  sleep Running
root@myshell /home/bill/Documents/myshell_test$ jobs
[1]+  12490  sleep Done
root@myshell /home/bill/Documents/myshell_test$ jobs
root@myshell /home/bill/Documents/myshell_test$ 
```
1. 用户在shell提示符下执行了命令 `sleep 20`，该命令会使当前进程挂起20秒钟。
2. 用户按下了 `Ctrl+Z` 组合键，发送了STGSTP信号，导致当前进程被挂起。
3. Shell显示了一个消息，指示进程已被停止，并显示了进程的相关信息：`[1] 12490    Stopped    sleep`。这表明进程的状态已经从Running变为Stopped，并且被标记为后台进程。
4. 用户在shell提示符下执行了命令 `bg`，该命令将最近被挂起的后台进程转为在后台运行。
5. Shell显示了一个消息，指示后台进程已经开始运行：`[1] sleep`。
6. 用户在shell提示符下执行了命令 `jobs`，该命令用于显示当前正在运行的作业列表。
7. Shell显示了一个消息，指示进程仍在运行：`[1]+  12490  sleep Running`。
8. 用户多次执行了命令 `jobs`，但是输出的结果始终显示进程仍在运行。
9. 用户再次执行了命令 `jobs`，此时输出的结果显示进程已经完成：`[1]+  12490  sleep Done`。这表明进程的状态已经从Running变为Done。
10. 用户再次执行了命令 `jobs`，此时输出的结果为空，表明没有正在运行的作业。

根据对话的展示，可以看出用户通过在shell中使用命令和myshell进行交互，实现了对进程的挂起、后台运行和作业状态的查询。这种交互方式可以方便地管理和监控正在运行的进程，并对它们进行必要的操作。

**cd命令**
```bash{.line-numbers}
root@myshell /home/bill/Documents/myshell_test$ cd
/home/bill/Documents/myshell_test
root@myshell /home/bill/Documents/myshell_test$ cd .
root@myshell /home/bill/Documents/myshell_test$ cd ..
root@myshell /home/bill/Documents$ cd /home
root@myshell /home$ 
```

1. 用户在shell提示符下执行了命令 `cd`，没有指定目录参数。这导致用户的当前工作目录保持不变：`/home/bill/Documents/myshell_test`。
2. 用户在shell提示符下执行了命令 `cd .`，表示切换到当前目录。由于当前目录是 `/home/bill/Documents/myshell_test`，所以用户的当前工作目录仍然保持不变：`/home/bill/Documents/myshell_test`。
3. 用户在shell提示符下执行了命令 `cd ..`，表示切换到上一级目录。由于上一级目录是 `/home/bill/Documents`，所以用户的当前工作目录变为：`/home/bill/Documents`。
4. 用户在shell提示符下执行了命令 `cd /home`，表示切换到 `/home` 目录。因此，用户的当前工作目录变为：`/home`。
5. 用户在shell提示符下执行了命令 `cd`，没有指定目录参数。这导致用户的当前工作目录保持不变：`/home`。

根据对话的展示，可以看出用户通过使用 `cd` 命令在shell中切换目录，改变了当前工作目录。用户可以使用相对路径（如 `.` 和 `..`）或绝对路径来指定目录。每次切换目录后，用户的当前工作目录会相应地改变。这使得用户可以在shell中方便地导航和操作文件系统中的不同目录。

**dir命令**
```bash{.line-numbers}
root@myshell /home/bill/Documents/myshell_test$ dir
signal_handler.h
temp.txt
myshell
myshell.h
test.sh
myshell.cpp
buildin_command.h
```
根据对话的展示，可以看出用户通过使用 `dir` 命令在shell中列出了当前目录中的文件和子目录。这使得用户可以查看当前目录中的内容，并了解可用的文件和子目录。

**echo命令**
```bash{.line-numbers}
bill@t14:~/Documents/myshell_test$ echo saf
saf
```

**exec命令**
```bash{.line-numbers}
root@myshell /home/bill/Documents/myshell_test$ exec ls
buildin_command.h  myshell  myshell.cpp  myshell.h  signal_handler.h  temp.txt  test.sh
```
执行外部命令


**exit命令**
```bash{.line-numbers}
bill@t14:~/Documents/myshell_test$ ./myshell 
welcome to myshell
root@myshell /home/bill/Documents/myshell_test$ exit
bye
```

**fg命令**
```bash{.line-numbers}
bill@t14:~/Documents/myshell_test$ sleep 10 &
[1] 13471
bill@t14:~/Documents/myshell_test$ fg
sleep 10
bill@t14:~/Documents/myshell_test$ 
```
1. 用户在shell提示符下执行了命令 `sleep 10 &`，该命令会使当前进程挂起10秒钟，并将其放入后台执行。
2. Shell显示了一个消息，指示进程已经在后台执行，并显示了进程的相关信息：`[1] 13471`。这表明进程的状态已经从Running变为后台执行，并且被标记为作业1。
3. 用户在shell提示符下执行了命令 `fg`，该命令用于将后台作业切换到前台执行。
4. Shell显示了进程的命令名称：`sleep 10`。这表明进程已经从后台切换到前台执行。
5. 用户在shell提示符下继续输入了命令。

根据对话的展示，可以看出用户通过使用 `sleep` 命令将一个进程放入后台执行，并使用 `fg` 命令将其切换到前台执行。这使得用户可以在后台执行的进程和前台执行的进程之间进行切换，并在需要时将后台进程切换到前台以进行交互。这对于同时执行多个任务或进程管理非常有用。

**help命令**
```bash{.line-numbers}
bill@t14:~/Documents/myshell_test$ ./myshell 
welcome to myshell
root@myshell /home/bill/Documents/myshell_test$ help
### myshell命令手册
- **命令：bg**
    将一个或多个后台暂停的进程切换到后台运行。

    用法：
    bg %[JOB_ID]: 将指定的后台进程切换到后台继续运行。
    bg: 将最近的一个后台进程切换到后台继续运行。

- **命令：fg**
    将一个后台暂停或运行的进程切换到前台运行。

    用法：
    fg %[JOB_ID]: 将指定的后台进程切换到前台继续运行。
    fg: 将最近的一个后台进程切换到前台继续运行。
 
- **命令：jobs**
    显示所有后台运行或挂起的进程的状态。
    用法：jobs


- **命令：cd**
    更改当前工作目录。

    用法：
    cd [目录路径]: 将当前工作目录切换到指定路径。
    cd: 显示当前工作目录路径。

- **命令：pwd**
    显示当前工作目录。

    用法：pwd

- **命令：clr**
    清空终端屏幕。
    用法：clr

- **命令：time**
    显示当前时间。
    用法：time
    ......省略
```

**jobs命令**
```bash{.line-numbers}
root@myshell /home/bill/Documents/myshell_test$ sleep 1000 &
[1] 13931
root@myshell /home/bill/Documents/myshell_test$ sleep 1000 &
[2] 13950
root@myshell /home/bill/Documents/myshell_test$ sleep 1000 &
[3] 13951
root@myshell /home/bill/Documents/myshell_test$ jobs
[1]   13931  sleep Running
[2]-  13950  sleep Running
[3]+  13951  sleep Running
```
1. 用户在shell提示符下执行了命令 `sleep 1000 &`，该命令会使当前进程挂起1000秒钟，并将其放入后台执行。
2. Shell显示了一个消息，指示进程已经在后台执行，并显示了进程的相关信息：`[1] 13931`。这表明进程的状态已经从Running变为后台执行，并且被标记为作业1。
3. 用户再次在shell提示符下执行了命令 `sleep 1000 &`，类似地将另一个进程放入后台执行。
4. Shell显示了一个消息，指示第二个进程已经在后台执行，并显示了进程的相关信息：`[2] 13950`。这表明第二个进程的状态已经从Running变为后台执行，并且被标记为作业2。
5. 用户又一次在shell提示符下执行了命令 `sleep 1000 &`，将第三个进程放入后台执行。
6. Shell显示了一个消息，指示第三个进程已经在后台执行，并显示了进程的相关信息：`[3] 13951`。这表明第三个进程的状态已经从Running变为后台执行，并且被标记为作业3。
7. 用户在shell提示符下执行了命令 `jobs`，该命令用于显示当前后台执行的作业列表。
8. Shell显示了后台作业的列表：
   ```bash
   [1]   13931  sleep Running
   [2]-  13950  sleep Running
   [3]+  13951  sleep Running
   ```
   这些是当前后台执行的作业的信息。作业1、2和3都是由 `sleep` 命令创建的进程，并且它们的状态都是Running。

根据对话的展示，可以看出用户通过使用 `sleep` 命令将多个进程放入后台执行，并使用 `jobs` 命令查看后台作业的列表。这使得用户可以同时执行多个后台任务，并随时查看它们的状态和相关信息。后台执行对于处理长时间运行的任务或同时执行多个任务非常有用。

**pwd命令**
```bash{.line-numbers}
root@myshell /home/bill/Documents/myshell_test$ pwd 
/home/bill/Documents/myshell_test
root@myshell /home/bill/Documents/myshell_test$ cd ..
root@myshell /home/bill/Documents$ pwd
/home/bill/Documents
```
1. 用户在shell提示符下执行了命令 `pwd`，该命令用于显示当前工作目录的路径。
2. Shell显示了当前工作目录的路径：`/home/bill/Documents/myshell_test`。这是当前所在的目录路径。
3. 用户在shell提示符下执行了命令 `cd ..`，该命令用于切换到当前目录的父目录。
4. Shell执行了切换目录的操作，并没有显示任何输出。
5. 用户在shell提示符下执行了命令 `pwd`，再次使用 `pwd` 命令来显示当前工作目录的路径。
6. Shell显示了当前工作目录的路径：`/home/bill/Documents`。这是切换到父目录后的新目录路径。

根据对话的展示，可以看出用户通过使用 `pwd` 命令查看当前工作目录的路径，并使用 `cd` 命令切换到父目录。这使得用户可以在文件系统中导航并更改当前工作目录。`pwd` 命令用于显示当前工作目录的路径，而 `cd` 命令用于切换目录。

**set命令**
```bash{.line-numbers}
root@myshell /home/bill/Documents$ set
SHELL=/home/bill/Documents/myshell_test
COLORTERM=truecolor
TERM_PROGRAM_VERSION=1.81.0
LANGUAGE=zh_CN:zh
LC_ADDRESS=zh_CN
LC_NAME=zh_CN
LC_MONETARY=zh_CN
PWD=/home/bill/Documents
LOGNAME=bill
XDG_SESSION_TYPE=tty
VSCODE_GIT_ASKPASS_NODE=/home/bill/.vscode-server/bin/6445d93c81ebe42c4cbd7a60712e0b17d9463e97/node
HOME=/home/bill
LC_PAPER=zh_CN
LANG=zh_CN.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=00:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.avif=01;35:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.webp=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:*~=00;90:*#=00;90:*.bak=00;90:*.old=00;90:*.orig=00;90:*.part=00;90:*.rej=00;90:*.swp=00;90:*.tmp=00;90:*.dpkg-dist=00;90:*.dpkg-old=00;90:*.ucf-dist=00;90:*.ucf-new=00;90:*.ucf-old=00;90:*.rpmnew=00;90:*.rpmorig=00;90:*.rpmsave=00;90:
GIT_ASKPASS=/home/bill/.vscode-server/bin/6445d93c81ebe42c4cbd7a60712e0b17d9463e97/extensions/git/dist/askpass.sh
SSH_CONNECTION=192.168.216.1 8271 192.168.216.129 22
VSCODE_GIT_ASKPASS_EXTRA_ARGS=
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_SESSION_CLASS=user
TERM=xterm-256color
LC_IDENTIFICATION=zh_CN
LESSOPEN=| /usr/bin/lesspipe %s
USER=bill
VSCODE_GIT_IPC_HANDLE=/run/user/1000/vscode-git-ccecddce55.sock
SHLVL=1
LC_TELEPHONE=zh_CN
LC_MEASUREMENT=zh_CN
XDG_SESSION_ID=4
XDG_RUNTIME_DIR=/run/user/1000
SSH_CLIENT=192.168.216.1 8271 22
DEBUGINFOD_URLS=https://debuginfod.ubuntu.com 
LC_TIME=zh_CN
LC_ALL=zh_CN.UTF-8
VSCODE_GIT_ASKPASS_MAIN=/home/bill/.vscode-server/bin/6445d93c81ebe42c4cbd7a60712e0b17d9463e97/extensions/git/dist/askpass-main.js
XDG_DATA_DIRS=/usr/share/gnome:/usr/local/share:/usr/share:/var/lib/snapd/desktop
BROWSER=/home/bill/.vscode-server/bin/6445d93c81ebe42c4cbd7a60712e0b17d9463e97/bin/helpers/browser.sh
PATH=/home/bill/.vscode-server/bin/6445d93c81ebe42c4cbd7a60712e0b17d9463e97/bin/remote-cli:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
LC_NUMERIC=zh_CN
TERM_PROGRAM=vscode
VSCODE_IPC_HOOK_CLI=/run/user/1000/vscode-ipc-11fb7b40-ba39-4377-8cbc-83abfdcbaec4.sock
OLDPWD=/home/bill/Documents
_=./myshell
PARENT=/home/bill/Documents/myshell_test
```
1. 用户在shell提示符下执行了命令 `set`，该命令用于显示当前shell的环境变量。
2. Shell显示了一系列环境变量的键值对，每个键值对都表示一个环境变量及其对应的值。这些环境变量包括 `SHELL`、和 `PARENT`等。
3. 每个环境变量的键值对表示了不同的信息，例如 `SHELL` 表示当前shell的路径，`PWD` 表示当前工作目录的路径，`USER` 表示当前用户的用户名等等。

根据对话的展示，可以看出用户通过使用 `set` 命令查看当前shell的环境变量。环境变量是在操作系统中存储配置信息的一种机制，它们可以影响程序的行为和运行环境。使用 `set` 命令可以列出当前shell中定义的所有环境变量及其对应的值。

**unset命令**
```bash{.line-numbers}
root@myshell /home/bill/Documents$ unset PARENT
root@myshell /home/bill/Documents$ set
SHELL=/home/bill/Documents/myshell_test
COLORTERM=truecolor
TERM_PROGRAM_VERSION=1.81.0
LANGUAGE=zh_CN:zh
LC_ADDRESS=zh_CN
LC_NAME=zh_CN
LC_MONETARY=zh_CN
PWD=/home/bill/Documents
LOGNAME=bill
XDG_SESSION_TYPE=tty
VSCODE_GIT_ASKPASS_NODE=/home/bill/.vscode-server/bin/6445d93c81ebe42c4cbd7a60712e0b17d9463e97/node
HOME=/home/bill
LC_PAPER=zh_CN
LANG=zh_CN.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=00:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.avif=01;35:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.webp=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:*~=00;90:*#=00;90:*.bak=00;90:*.old=00;90:*.orig=00;90:*.part=00;90:*.rej=00;90:*.swp=00;90:*.tmp=00;90:*.dpkg-dist=00;90:*.dpkg-old=00;90:*.ucf-dist=00;90:*.ucf-new=00;90:*.ucf-old=00;90:*.rpmnew=00;90:*.rpmorig=00;90:*.rpmsave=00;90:
GIT_ASKPASS=/home/bill/.vscode-server/bin/6445d93c81ebe42c4cbd7a60712e0b17d9463e97/extensions/git/dist/askpass.sh
SSH_CONNECTION=192.168.216.1 8271 192.168.216.129 22
VSCODE_GIT_ASKPASS_EXTRA_ARGS=
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_SESSION_CLASS=user
TERM=xterm-256color
LC_IDENTIFICATION=zh_CN
LESSOPEN=| /usr/bin/lesspipe %s
USER=bill
VSCODE_GIT_IPC_HANDLE=/run/user/1000/vscode-git-ccecddce55.sock
SHLVL=1
LC_TELEPHONE=zh_CN
LC_MEASUREMENT=zh_CN
XDG_SESSION_ID=4
XDG_RUNTIME_DIR=/run/user/1000
SSH_CLIENT=192.168.216.1 8271 22
DEBUGINFOD_URLS=https://debuginfod.ubuntu.com 
LC_TIME=zh_CN
LC_ALL=zh_CN.UTF-8
VSCODE_GIT_ASKPASS_MAIN=/home/bill/.vscode-server/bin/6445d93c81ebe42c4cbd7a60712e0b17d9463e97/extensions/git/dist/askpass-main.js
XDG_DATA_DIRS=/usr/share/gnome:/usr/local/share:/usr/share:/var/lib/snapd/desktop
BROWSER=/home/bill/.vscode-server/bin/6445d93c81ebe42c4cbd7a60712e0b17d9463e97/bin/helpers/browser.sh
PATH=/home/bill/.vscode-server/bin/6445d93c81ebe42c4cbd7a60712e0b17d9463e97/bin/remote-cli:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
LC_NUMERIC=zh_CN
TERM_PROGRAM=vscode
VSCODE_IPC_HOOK_CLI=/run/user/1000/vscode-ipc-11fb7b40-ba39-4377-8cbc-83abfdcbaec4.sock
OLDPWD=/home/bill/Documents
_=./myshell
```
看到最后一行的PARENT变量改变了。


**umask命令**
```bash{.line-numbers}
root@myshell /home/bill/Documents$ umask
0002
root@myshell /home/bill/Documents$ umask 122
root@myshell /home/bill/Documents$ umask
0122
```
1. 用户在shell提示符下执行了命令 `umask`，该命令用于显示当前的文件创建权限掩码。
2. Shell显示了当前的文件创建权限掩码为 `0002`。
3. 用户在shell提示符下执行了命令 `umask 122`，该命令用于设置文件创建权限掩码。
4. Shell执行了设置文件创建权限掩码的操作，并没有显示任何输出。
5. 用户在shell提示符下再次执行了命令 `umask`，用于显示当前的文件创建权限掩码。
6. Shell显示了当前的文件创建权限掩码为 `0122`。

根据对话的展示，可以看出用户通过使用 `umask` 命令来查看和设置文件创建权限掩码。文件创建权限掩码是一个八进制数，用于确定在创建新文件或目录时所应用的默认权限。使用 `umask` 命令可以查看当前的文件创建权限掩码，也可以通过提供一个新的权限掩码值来设置文件创建权限掩码。


#### 源程序
myshell.cpp
```c++
#include "myshell.h"
#include "signal_handler.h"
#include "buildin_command.h"

using namespace std;

int main(int argc, char **argv) {
    // 进程表的作用在于处理多进程的情况，当用户输入命令时，父进程会创建子进程来执行命令，父进程需要记录子进程的pid，以便于管理
    Jobs_init();    // 初始化jobs列表，并注册信号处理函数

    if (argc != 1) {    // 文件处理命令
        fstream infile;
        infile.open(argv[1], ios::in);  // 打开文件
        if (!infile) {  // 打开失败
            fprintf(stderr, "open file failed\n");
            exit(1);    
        }
        string cmd; // 读取文件
        while (getline(infile, cmd)) {  // 逐行读取
            vector<Command> *cmds = new vector<Command>;    
            arg_parser(cmd, cmds);  // 解析命令
            run_commands(*cmds);    // 执行命令
            delete cmds;            // 释放内存
        }
        infile.close(); // 关闭文件
        exit(0);        // 退出程序
    }

    cout << "welcome to myshell" << endl;   // 欢迎语
    rootpath = getcwd(NULL, 0); // 获取当前路径
    setenv("SHELL", rootpath.c_str(), 1);   // 设置环境变量

    string cmd;
    cout << "root@myshell " << getcwd(NULL, 0) << "$ "; // 打印提示符
    getline(cin, cmd);  // 读取命令
    while (cmd != "exit") { // 退出命令
        vector<Command> *cmds = new vector<Command>;     
        arg_parser(cmd, cmds);  // 解析命令
        run_commands(*cmds);    // 执行命令
        cout << "root@myshell " << getcwd(NULL, 0) << "$ "; // 打印提示符
        getline(cin, cmd);  // 读取命令
        delete cmds;    // 释放内存
    }
    cout << "bye" << endl;  // 再见语
    return 0;
}

int find_job(pid_t pid) {   // 根据pid查找job
    int index = -1;     // 未找到返回-1
    for (int i = 0; i < jobs_info->total_jobs; i++) {   // 遍历jobs
        if (jobs[i].pid == pid) {   // 找到
            index = i;
            break;
        }
    }
    return index;   // 返回索引
}

int find_job_by_bg(int bg_index_) { // 根据bg_index查找job
    int index = -1;    // 未找到返回-1
    for (int i = 0; i < jobs_info->total_jobs; i++) {   // 遍历jobs
        if (jobs[i].bg_index == bg_index_) {    // 找到
            index = i;
            break;
        }
    }
    return index;   // 返回索引
}

void delete_job(pid_t pid) {    // 删除job
    int i = find_job(pid);  // 查找job
    // if (i == -1) cout << "not found" << endl;
    bool bg_flag = !jobs[i].is_FG;  // 判断是否为后台进程
    if (i != -1) {  // 找到
        for ( ; i < jobs_info->total_jobs - 1; i++) {   // 后面的job前移
            if (bg_flag && !jobs[i + 1].is_FG) {
                jobs[i + 1].bg_index--; // 后台进程bg_index前移
            }
            jobs[i] = jobs[i + 1];
        }
        jobs_info->total_jobs--;
        if (bg_flag) {  // 后台进程
            jobs_info->bg_jobs--;   // bg_jobs前移
        }
    }
}

void arg_parser(std::string &str_in, vector<Command> *cmd) {    // 解析命令
    cmd->clear();   // 清空命令
    const char delimiters[] = " \n\t";  // 分隔符
    const char* char_str = str_in.c_str();  // 转换为char*
    char* token = strtok(const_cast<char*>(char_str), delimiters);  // 分割命令，按照空格、换行、制表符分割

    vector<string> tmp; // 临时存储命令
    while (token != NULL) {     
        // printf("%s\n", token);  
        tmp.push_back(token);   // 存储命令
        token = strtok(NULL, delimiters);   // 继续分割
    }  

    Command *item = new Command();  // 临时存储命令
    int rein_flag = 0, reout_flag = 0, reapp_flag = 0;  // 重定向标志
    for (int i = 0; i < tmp.size(); i++) {  // 遍历命令
        if (rein_flag) {    // 输入重定向
            rein_flag = 0;  // 重定向标志置0
            item->inPath = tmp[i];  // 设置输入重定向路径
        }
        else if (reout_flag) {  // 输出重定向
            reout_flag = 0;     // 重定向标志置0
            item->outPath = tmp[i]; // 设置输出重定向路径
        }
        else if (tmp[i] == "<") {   // 输入重定向
            item->reIn = true;      // 设置输入重定向标志
            rein_flag = 1;          // 设置重定向标志
        }
        else if (tmp[i] == ">") {   // 输出重定向
            item->reOut = true;     // 设置输出重定向标志
            reout_flag = 1;         // 设置重定向标志
        }
        else if (tmp[i] == ">>") {  // 追加重定向    
            item->reApp = true;     // 设置追加重定向标志
            reout_flag = true;      // 设置重定向标志
        }
        else if (tmp[i] == "&") {   // 后台运行
            item->is_BG = true;     // 设置后台运行标志
        }
        else if (tmp[i] == "|") {   // 管道
            item->pipOut = true;    // 设置管道输出标志
            cmd->push_back(*item);  // 存储命令
            item->cmd_clear();      // 清空命令
            item->pipIn = true;     // 设置管道输入标志
        }
        else {  // 普通命令
            item->content.push_back(tmp[i]);
        }
    }
    cmd->push_back(*item);  
    return; 
}

bool is_buildin_command(Command &cmd) {   // 判断是否为内建命令
    if (cmd.content[0] == "umask" ||    // 内建命令     
        cmd.content[0] == "cd" ||       
        cmd.content[0] == "jobs" ||
        cmd.content[0] == "help" ||
        cmd.content[0] == "bg" ||
        cmd.content[0] == "exec" ||
        cmd.content[0] == "fg" ||
        cmd.content[0] == "unset" || 
        cmd.content[0] == "clr") {
            return true;
        }
    return false;
}

void run_commands(vector<Command> &cmds) {  // 执行命令
    if (is_buildin_command(cmds[0])) {  // 内建命令
        execute_buildin_command(cmds[0]);   // 执行内建命令
        return;
    }

    pid_t pid = fork(); // 创建子进程，子进程用于执行命令，父进程用于管理job
    if (pid < 0) {  // 创建失败
        fprintf(stderr, "create process failed");
        exit(1);
    }
    else if (pid == 0) { // 子进程，用于执行命令
        while (true) {   // 必须等待父进程将job添加到进程列表中
            usleep(1);
            if (find_job(getpid()) == -1) { // 父进程还未将job添加到进程列表中
                usleep(1);
            } else {
               break;
            }
        }
        // cout << "child process:" << getpid() << endl;
        if (!cmds[0].pipIn && !cmds[0].pipOut) { // 普通命令，无管道
            if (cmds[0].reIn) { // 输入重定向
                close(0);   // 关闭标准输入
                int Fd = open(cmds[0].inPath.c_str(), O_RDONLY);    // 打开文件
                check_file_error(Fd);   // 检查文件是否打开成功
            }
            if (cmds[0].reOut) {    // 输出重定向
                close(1);   // 关闭标准输出
                int Fd = open(cmds[0].outPath.c_str(), O_WRONLY | O_CREAT | O_TRUNC, 0666); // 打开文件
                check_file_error(Fd);   // 检查文件是否打开成功
            }
            if (cmds[0].reApp) {    // 追加重定向
                close(1);   // 关闭标准输出
                int Fd = open(cmds[0].outPath.c_str(), O_RDWR | O_CREAT | O_APPEND, 0666);  // 打开文件
                check_file_error(Fd);   // 检查文件是否打开成功
            }
            if (!execute_buildin_command(cmds[0])) {    // 执行内建命令
                char* argv[cmds[0].content.size() + 1]; // 执行普通命令
                for (int i = 0; i < cmds[0].content.size(); i++) {  // 将命令转换为char*
                    argv[i] = const_cast<char*>(cmds[0].content[i].c_str());
                }
                argv[cmds[0].content.size()] = NULL;    // 最后一个参数为NULL
                if (execvp(argv[0], argv) == -1) {  // 执行命令
                    fprintf(stderr, "execvp failed: %s\n", strerror(errno));
                    exit(1);
                }
            }
            close(0);   // 恢复标准输入
            close(1);   // 恢复标准输出
        }
        else { // 管道
            for (int i = 0; i < cmds.size() - 1; i++) {
                pid_t pid2 = fork();    // 递归地创建子进程
                if (pid2 < 0) {
                    fprintf(stderr, "create process failed");
                    exit(1);
                }
                else if (pid2 == 0) { // 子进程处理管道
                    if (i != 0) {   // 不是第一个命令
                        close(0);   // 关闭标准输入
                        int Fd = open(pip_file, O_RDONLY);  // 打开管道文件
                    }
                    if (cmds[i].reIn) { // 输入重定向
                        close(0);   // 关闭标准输入
                        int Fd = open(cmds[i].inPath.c_str(), O_RDONLY);    // 打开文件
                        check_file_error(Fd);   // 检查文件是否打开成功
                    }
                    if (cmds[i].reOut) {    // 输出重定向
                        close(1);           // 关闭标准输出
                        int Fd = open(cmds[i].outPath.c_str(), O_WRONLY | O_CREAT | O_TRUNC, 0666); // 打开文件
                        check_file_error(Fd);   // 检查文件是否打开成功
                    }
                    if (cmds[i].reApp) {    // 追加重定向
                        close(1);           // 关闭标准输出
                        int Fd = open(cmds[i].outPath.c_str(), O_RDWR | O_CREAT | O_APPEND, 0666);  // 打开文件
                        check_file_error(Fd);   // 检查文件是否打开成功
                    }
                    close(1);   // 关闭标准输出
                    remove(pip_file);   // 删除管道文件
                    int Fd = open(pip_file, O_WRONLY | O_CREAT | O_TRUNC, 0666);    // 创建管道文件
                    check_file_error(Fd);   // 检查文件是否打开成功
                    if (!execute_buildin_command(cmds[i])) {    // 执行内建命令
                        char* argv[cmds[i].content.size() + 1]; // 执行普通命令
                        for (int j = 0; j < cmds[i].content.size(); j++) {  // 将命令转换为char*
                            argv[j] = const_cast<char*>(cmds[i].content[j].c_str());    
                        }
                        argv[cmds[i].content.size()] = NULL;    // 最后一个参数为NULL
                        if (execvp(argv[0], argv) == -1) {      // 执行命令
                            fprintf(stderr, "execvp failed: %s\n", strerror(errno));
                            exit(1);
                        }
                    }
                }
                else {
                    waitpid(pid2, NULL, 0); // 等待子进程执行完毕
                }
            }
            // 处理最后一条命令
            close(0);  
            int Fd = open(pip_file, O_RDONLY);  // 打开管道文件
            check_file_error(Fd);               // 检查文件是否打开成功
            if (cmds[cmds.size() - 1].reOut) {  // 输出重定向
                close(1);
                int Fd = open(cmds[cmds.size() - 1].outPath.c_str(), O_WRONLY | O_CREAT | O_TRUNC, 0666);   // 打开文件
                check_file_error(Fd);
            }
            if (cmds[cmds.size() - 1].reApp) {
                close(1);
                int Fd = open(cmds[cmds.size() - 1].outPath.c_str(), O_RDWR | O_CREAT | O_APPEND, 0666);    // 打开文件
                check_file_error(Fd);
            }
            if (!execute_buildin_command(cmds[cmds.size() - 1])) {  // 执行内建命令
                char* argv[cmds[cmds.size() - 1].content.size() + 1];   // 执行普通命令
                for (int i = 0; i < cmds[cmds.size() - 1].content.size(); i++) {    // 将命令转换为char*
                    argv[i] = const_cast<char*>(cmds[cmds.size() - 1].content[i].c_str());
                }
                argv[cmds[cmds.size() - 1].content.size()] = NULL;
                if (execvp(argv[0], argv) == -1) {
                    fprintf(stderr, "execvp failed: %s\n", strerror(errno));    // 执行失败
                    exit(1);
                }
            }
            close(0);   
            close(1);
        }

    }
    else {  // 父进程，添加进程表
        jobs_info->total_jobs++;    // 进程数加1
        if (jobs_info->total_jobs == MAX_JOB) {   // 进程数超过最大值
            fprintf(stderr, "too many jobs\n");     
            exit(1);
        }

        jobs[jobs_info->total_jobs - 1].set_job(pid, cmds[0].content[0].c_str(), !cmds[cmds.size() - 1].is_BG, false, false, -1); // 添加进程
        setenv("PARENT", rootpath.c_str(), 1); // 设置环境变量
        if (cmds[cmds.size() - 1].is_BG) {  // 后台进程
            jobs[jobs_info->total_jobs - 1].bg_index = (++jobs_info->bg_jobs); // 设置bg_index
            printf("[%d] %d\n", jobs[jobs_info->total_jobs - 1].bg_index, jobs[jobs_info->total_jobs - 1].pid); // 打印后台进程信息
        }
        jobs[jobs_info->total_jobs - 1].is_running = true;  // 设置进程状态
        if (!cmds[cmds.size() - 1].is_BG) { // 前台进程
            waitpid(pid, NULL, WUNTRACED);  // 等待子进程执行完毕
            if (jobs[jobs_info->total_jobs - 1].is_running) {   // 子进程未被暂停
                delete_job(pid);    // 删除job
            }
        }
        else {  // 后台进程
            waitpid(pid, NULL, WNOHANG);    // 无需等待子进程执行完毕
            // cout << "wait no hang" << endl;
        }
    }
}

void check_file_error(int Fd) {   // 检查文件是否打开成功
    if (Fd == -1) { // 打开失败
        std::cerr << "Failed to open file: "    // 打印错误信息
        << std::strerror(errno) << std::endl;   // 打印错误信息
    }
}

bool execute_buildin_command(Command &cmd) {    // 执行内建命令
    if (cmd.content.empty()) {      // 命令为空
        fprintf(stderr, "command is illegal\n");    // 打印错误信息
        exit(1);                            // 退出程序
    }
    else if (cmd.content[0] == "bg") BG(cmd);   // 执行内建命令
    else if (cmd.content[0] == "fg") FG(cmd);   
    else if (cmd.content[0] == "jobs") JOBS();
    else if (cmd.content[0] == "cd") CD(cmd.content);
    else if (cmd.content[0] == "pwd") PWD();
    else if (cmd.content[0] == "clr") CLR();
    else if (cmd.content[0] == "time") TIME();
    else if (cmd.content[0] == "umask") UMASK(cmd.content);
    else if (cmd.content[0] == "dir") DIR_(cmd.content);
    else if (cmd.content[0] == "set") SET();
    else if (cmd.content[0] == "echo") ECHO(cmd.content);
    else if (cmd.content[0] == "exec") EXEC(cmd.content);
    else if (cmd.content[0] == "test") TEST(cmd.content);
    else if (cmd.content[0] == "unset") UNSET(cmd.content);
    else if (cmd.content[0] == "help") {
        cmd.content[0] = "more";
        cmd.content.push_back("help.txt");
        char* argv[cmd.content.size() + 1]; // 执行普通命令
        for (int i = 0; i < cmd.content.size(); i++) {  // 将命令转换为char*
            argv[i] = const_cast<char*>(cmd.content[i].c_str());
        }
        argv[cmd.content.size()] = NULL;    // 最后一个参数为NULL
        if (execvp(argv[0], argv) == -1) {  // 执行命令
            fprintf(stderr, "execvp failed: %s\n", strerror(errno));
            exit(1);
        }
    }
    else {
        return false;
    }
    return true;
}
```

myshell.h
```c++
#pragma once

#include <iostream>
#include <vector>
#include <string>
#include <fstream>
#include <stdio.h>
#include <unistd.h>
#include <ctime>
#include <cerrno>
#include <cstring>
#include <sys/types.h>
#include <sys/stat.h>
#include <dirent.h>
#include <sys/shm.h>
#include <wait.h>
#include <fcntl.h>
#include <signal.h>
#include <iomanip>

using namespace std;

#define MAX_NAME_LENGTH 64  // max length of job name
#define MAX_JOB 64          // max number of jobs

class Job {     // job struct
public:
    pid_t pid = 0;  // 进程号
    int bg_index;   // 后台进程号
    char job_name[MAX_NAME_LENGTH]; // 进程名
    bool is_FG;    // 是否为前台进程
    bool is_running;    // 是否正在运行
    bool end_of_run;    // 是否运行结束

    void set_job(pid_t pid_, const char* name, bool fg_, bool run, bool end, int bg_index_);    // 设置进程信息   
};

void Job::set_job(pid_t pid_, const char* name, bool fg_, bool run, bool end, int bg_index_) {
    pid = pid_;
    strcpy(job_name, name);
    is_FG = fg_;
    is_running = run;
    end_of_run = end;
    bg_index = bg_index_;
}

struct Jobs_Info    
{
    int total_jobs; // 总进程数
    int bg_jobs;    // 后台进程数
};

Jobs_Info *jobs_info;   // 共享内存

Job *jobs;  // 作业数组

class Command { // 命令类
public:
    vector<string> content; // 命令内容
    bool is_BG; // 是否为后台进程
    bool pipIn; // 是否有管道输入
    bool pipOut;    // 是否有管道输出
    bool reIn;      // 是否有重定向输入
    bool reOut;     // 是否有重定向输出
    bool reApp;     // 是否有重定向追加输出
    string inPath;     // 输入重定向路径
    string outPath;     // 输出重定向路径

    Command() { // 初始化
        is_BG = false;
        pipIn = false;
        pipOut = false;
        reIn = false;
        reOut = false;
        reApp = false;
        inPath = "";
        outPath = "";
    }

    void cmd_clear();   // 清空命令内容
    ~Command() {}
};

void Command::cmd_clear() { // 清空命令内容
    content.clear();    // 清空命令内容
    is_BG = false;  
    pipIn = false;  
    pipOut = false;
    reIn = false;
    reOut = false;
    reApp = false;
    inPath = "";
    outPath = "";
}

int BG_flag = 0;

char pip_file[16] = "temp.txt";
string rootpath;

struct sigaction new_act;
struct sigaction old_act;


void arg_parser(std::string &str_in, vector<Command> *cmd);
bool is_buildin_command(Command &cmd);
bool execute_buildin_command(Command &cmd);
void run_commands(vector<Command> &cmds);
int find_job(pid_t pid);
void delete_job(pid_t pid);
void check_file_error(int Fd);
int find_job_by_bg(int bg_index_);
```

buildin_command.h

```c++
#pragma once

#include "signal_handler.h"


void Jobs_init() {  // 初始化进程表,初始化共享内存，并注册信号处理函数
    int shmid = shmget((key_t)1145, sizeof(Job*) * MAX_JOB, 0666 | IPC_CREAT); // 创建共享内存
    void* shm = NULL;   // 共享内存的首地址
    shm = shmat(shmid, 0, 0);   // 将共享内存连接到当前进程的地址空间
    jobs = (Job*)shm;   // 将共享内存首地址转换为进程表的首地址

    jobs[0].set_job(0, "myshell", false, true, false, 0);   // 初始化进程表第一项，代表shell进程
    
    int shmid1 = shmget((key_t)1451, sizeof(Jobs_Info*), 0666 | IPC_CREAT); // 创建共享内存
    void* shm1 = NULL;  // 共享内存的首地址
    shm1 = shmat(shmid1, 0, 0); // 将共享内存连接到当前进程的地址空间
    jobs_info = (Jobs_Info*)shm1;   // 将共享内存首地址转换为进程表的首地址
    jobs_info->bg_jobs = 0;         // 初始化后台进程数
    jobs_info->total_jobs = 1;      // 初始化进程总数
    signal_init();                  // 注册信号处理函数
}

// 将后台进程转为前台进程（无参数时默认最近的后台进程）
void BG(Command& cmd) {
    //cout << "bg" << endl;
    if (cmd.content.size() == 1) {  // 无参时默认最近的后台进程
        int i;
        for (i = 1; i < jobs_info->total_jobs; i++) {   // 找到最近的后台进程
            if (!jobs[i].is_FG && !jobs[i].is_running && !jobs[i].end_of_run) {
                jobs[i].is_running = true;
                jobs[i].is_FG = false;
                BG_flag = true;
                kill(jobs[i].pid, SIGCONT); // 向进程发送SIGCONT信号，使其继续运行
                //cout << i << "-> ";
                printf("[%d] %s\n", jobs[i].bg_index, jobs[i].job_name);    // 输出后台进程信息
            }
        }
        if (jobs_info->bg_jobs == 0) {  // 没有后台进程
            printf("No stopped jobs\n");    
            return;
        }
    }
    else {  // 带参时根据参数指定的后台进程
        if (cmd.content[1][0] == '%') { // 参数以%开头
            int id = atoi(cmd.content[1].substr(1).c_str());    // 获取参数中的进程号
            if (id > jobs_info->total_jobs || id < 0) { // 进程号不合法
                printf("No such job\n");
                return;
            }
            if (!jobs[id].is_FG && !jobs[id].is_running && !jobs[id].end_of_run) {  // 进程号合法且为后台进程
                jobs[id].is_running = true;
                jobs[id].is_FG = false;
                BG_flag = true;
                kill(jobs[id].pid, SIGCONT);    // 向进程发送SIGCONT信号，使其继续运行
                printf("[%d] %s\n", jobs[id].bg_index, jobs[id].job_name);  // 输出后台进程信息
            }
            else {
                printf("No such job\n");    // 进程号合法但不是后台进程
                return;
            }
        }
        else {
            printf("invalid input\n");  // 参数不合法
            return;
        }
    }
}

// 无参数时默认最近的后台进程
void FG(Command& cmd) { 
    int bg_id = -1; // 要转到前台的后台进程的进程号
    if (cmd.content.size() == 1) {  // 无参时默认最近的后台进程
        int cnt = jobs_info->total_jobs - 1;    // 从后往前找
        for (; cnt > 0; cnt--) {    // 找到最近的后台进程
            if (!jobs[cnt].is_FG && !jobs[cnt].end_of_run){ // 找到最近的后台进程
                bg_id = cnt;    // 记录进程号
                break;  // 找到后跳出循环
            }
        }
    } 
    else if (cmd.content.size() == 2) { // 带参时根据参数指定的后台进程
        if (cmd.content[1][0] == '%') { // 参数以%开头
            int id = atoi(cmd.content[1].substr(1).c_str());    // 获取参数中的进程号
            id = find_job_by_bg(id);    // 根据进程号找到对应的进程
            if (id > jobs_info->total_jobs || id < 0) { // 进程号不合法
                printf("No such job\n");    
                return;
            }
            if (!jobs[id].is_FG && !jobs[id].end_of_run) {  // 进程号合法且为后台进程
                bg_id = id;
            }
            else {  // 进程号合法但不是后台进程
                printf("No such job\n");    
                return;
            }
        }
        else {  // 参数不合法
            printf("invalid input\n");
            return;
        }
    }
    else {  // 参数过多
        printf("fg: too many arguments\n");
        return;
    }

    if (bg_id == -1) {  // 没有后台进程
        printf("No background jobs\n");
        return;
    }
    else {  
        // 转到前台执行，首先要把该进程移到进程表的最后，代表是最新的进程（防止CTRL+Z后再次执行fg时，进程表中的顺序不对）
        Job tmp = jobs[bg_id];  
        for (int i = bg_id + 1; i < jobs_info->total_jobs - 1; i++) {   // 从后往前移动
            jobs[i] = jobs[i + 1];  // 后面的进程往前移动
            if (!jobs[i].is_FG) {   // 如果是后台进程，进程号要减一
                jobs[i].bg_index--; 
            }
        }
        jobs[jobs_info->total_jobs - 1] = tmp;  // 将该进程移到最后
        jobs[jobs_info->total_jobs - 1].bg_index = -1;  // 进程号置为-1，代表是前台进程
        if (jobs[jobs_info->total_jobs - 1].is_running) {   // 如果是运行中的进程，直接将其转到前台
            jobs[jobs_info->total_jobs - 1].is_FG = true;   
            cout << jobs_info->total_jobs - 1 << ":" << jobs[jobs_info->total_jobs - 1].pid << endl;    // 输出进程号
            cout << jobs[jobs_info->total_jobs - 1].job_name << endl;   // 输出进程名
            waitpid(jobs[jobs_info->total_jobs - 1].pid, NULL, 0);  // 等待进程结束
        }
        else {  // 如果是挂起的进程，先将其转到前台，再等待进程结束
            jobs[jobs_info->total_jobs - 1].is_FG = true;
            jobs[jobs_info->total_jobs - 1].is_running = true;
            jobs[jobs_info->total_jobs - 1].end_of_run = false;
            cout << jobs_info->total_jobs - 1 << ":" << jobs[jobs_info->total_jobs - 1].pid << endl;
            cout << jobs[jobs_info->total_jobs - 1].job_name << endl;
            kill(jobs[jobs_info->total_jobs - 1].pid, SIGCONT);
            waitpid(jobs[jobs_info->total_jobs - 1].pid, NULL, 0);
        }
    }
}

// 打印后台运行和挂起的进程
void JOBS() {
    vector<pid_t> del_pid_list; // 记录要删除的进程号
    for (int i = 1; i < jobs_info->total_jobs; i++) {   // 遍历进程表
        if (!jobs[i].is_FG) {   // 如果是后台进程
            if (jobs[i].end_of_run) {   // 如果进程已经结束
                if (jobs[i].bg_index == jobs_info->bg_jobs) {   // 如果是最近的后台进程
                    cout << "[" << jobs[i].bg_index << "]+ "<< setw(6) << jobs[i].pid << "  " << jobs[i].job_name << " Done" << endl;
                }
                else if (jobs[i].bg_index == jobs_info->bg_jobs - 1) { // 如果是次近的后台进程
                    cout << "[" << jobs[i].bg_index << "]- "<< setw(6) << jobs[i].pid << "  " << jobs[i].job_name << " Done" << endl;
                }
                else {  // 如果是其他后台进程
                    cout << "[" << jobs[i].bg_index << "]  "<< setw(6) << jobs[i].pid << "  " << jobs[i].job_name << " Done" << endl;
                }
                
                del_pid_list.push_back(jobs[i].pid);    // 记录要删除的进程号
            }
            else if (!jobs[i].is_running) { // 如果进程挂起
                if (jobs[i].bg_index == jobs_info->bg_jobs) {   // 如果是最近的后台进程
                    cout << "[" << jobs[i].bg_index << "]+ "<< setw(6) << jobs[i].pid << "  " << jobs[i].job_name << " Stopped" << endl;
                }
                else if (jobs[i].bg_index == jobs_info->bg_jobs - 1) {  // 如果是次近的后台进程
                    cout << "[" << jobs[i].bg_index << "]- "<< setw(6) << jobs[i].pid << "  " << jobs[i].job_name << " Stopped" << endl;
                }
                else {  // 如果是其他后台进程
                    cout << "[" << jobs[i].bg_index << "]  "<< setw(6) << jobs[i].pid << "  " << jobs[i].job_name << " Stopped" << endl;
                }
            }
            else {  // 如果进程正在运行
                if (jobs[i].bg_index == jobs_info->bg_jobs) {   // 如果是最近的后台进程
                    cout << "[" << jobs[i].bg_index << "]+ "<< setw(6) << jobs[i].pid << "  " << jobs[i].job_name << " Running" << endl;
                }   
                else if (jobs[i].bg_index == jobs_info->bg_jobs - 1) {  // 如果是次近的后台进程
                    cout << "[" << jobs[i].bg_index << "]- "<< setw(6) << jobs[i].pid << "  " << jobs[i].job_name << " Running" << endl;
                }
                else {  // 如果是其他后台进程
                    cout << "[" << jobs[i].bg_index << "]  "<< setw(6) << jobs[i].pid << "  " << jobs[i].job_name << " Running" << endl;
                }
            }
        }
    }
    for (int i = 0; i < del_pid_list.size(); i++) {
        delete_job(del_pid_list[i]);    // 删除进程
    }
}

// 无参时输出当前目录，带参时转移到指定目录
void CD(vector<string>& content) {
    // printf("content size: %d\n", content.size());
    if (content.size() == 1) {
        char* path = getcwd(NULL, 0);
        printf("%s\n", path);
        free(path);
    }
    else if (content.size() == 2) { // 带参时转移到指定目录
        if (chdir(content[1].c_str()) == -1) {  // 转移失败
            printf("cd: %s: No such file or directory\n", content[1].c_str());
        } else {    // 转移成功
            char* path = getcwd(NULL, 0);
            setenv("PWD", path, 1); // 设置环境变量
            free(path); 
        }
    }
    else {
        printf("cd: too many arguments\n");
    }
}

void PWD() {    // 输出当前目录
    cout << getcwd(NULL, 0) << endl;
}

// 清屏
void CLR() {
    printf("\033[2J");  // 清屏
    printf("\033[0;0f");    // 光标移动到左上角
    printf("\e[1;1H");  // 光标移动到左上角
}

// 输出当前时间
void TIME() {
    time_t t = time(NULL);  
    struct tm* local = localtime(&t);   // 获取本地时间
    printf("%02d:%02d:%02d\n", local->tm_hour, local->tm_min, local->tm_sec);   // 输出时间
}


// 掩码设置
void UMASK(vector<string>& content) {
    if (content.size() == 1) {  // 无参时输出当前掩码
        printf("%04o\n", umask(0));
        umask(umask(0)); // 恢复原来的掩码
    }
    else if (content.size() == 2) { // 带参时设置掩码
        int mask = 0;
        for (int i = 0; i < content[1].size(); i++) {   // 将字符串转换为数字
            mask = mask * 8 + content[1][i] - '0';      
        }
        umask(mask);    // 设置掩码
    }
    else {  // 参数过多
        printf("umask: too many arguments\n");  
    }
}

// 列出当前目录下的文件和子目录
void DIR_(vector<string>& content) {
    if (content.size() == 1) { // 无参时输出当前目录
        DIR* dir = opendir(".");    
        struct dirent* ptr;
        while ((ptr = readdir(dir)) != NULL) {  // 读取目录项
            if (ptr->d_name[0] != '.') {    // 忽略隐藏文件
                printf("%s\n", ptr->d_name);
            }
        }
        closedir(dir);  // 关闭目录
    }
    else if (content.size() == 2) { // 带参时输出指定目录
        DIR* dir = opendir(content[1].c_str()); // 打开指定目录
        if (dir == NULL) {  // 目录不存在
            printf("dir: %s: No such file or directory\n", content[1].c_str());
            return;
        }
        struct dirent* ptr;
        while ((ptr = readdir(dir)) != NULL) {  // 读取目录项
            if (ptr->d_name[0] != '.') {    // 忽略隐藏文件
                printf("%s\n", ptr->d_name);    
            }
        }
        closedir(dir);  // 关闭目录
    }
    else {  // 参数过多
        printf("dir: too many arguments\n");
    }
}

// 输出环境变量
void SET() {
    extern char** environ;
    for (int i = 0; environ[i] != NULL; i++) {  // 遍历环境变量
        printf("%s\n", environ[i]);
    }
}

// 输出参数
void ECHO(vector<string>& content) {    
    for (int i = 1; i < content.size(); i++) {  // 遍历参数
        printf("%s ", content[i].c_str());
    }
    printf("\n");
}

// 执行外部命令
void EXEC(vector<string>& content) {
    char** argv = new char*[content.size() + 1];    // 参数列表
    for (int i = 1; i < content.size(); i++) {  // 将参数转换为C风格字符串
        argv[i - 1] = new char[content[i].size() + 1];  
        strcpy(argv[i - 1], content[i].c_str());    
    }
    argv[content.size()] = NULL;
    if (!execvp(argv[0], argv)) {   // 执行命令
        printf("exec: %s: No such file or directory\n", argv[0]);
    }
    for (int i = 0; i < content.size(); i++) {      // 释放内存
        delete[] argv[i];
    }
    delete[] argv;  
    exit(0);
}

// 比较字符串或数字
void TEST(vector<string> content) { 
    if (content.size() == 4) {  // 比较两个字符串或数字
        if (content[2] == "-eq" || content[2] == "=") { // 比较字符串
            if (content[1] == content[3]) { // 字符串相等
                printf("true\n");
            }
            else {
                printf("false\n");
            }
        }
        else if (content[2] == "-ne" || content[2] == "!=") {   
            if (content[1] != content[3]) { // 字符串不相等
                printf("true\n");
            }
            else {
                printf("false\n");
            }
        }
        else if (content[2] == "-gt") {
            if (content[1] > content[3]) {  // 字符串大于
                printf("true\n");
            }
            else {
                printf("false\n");
            }
        }
        else if (content[2] == "-lt") {
            if (content[1] < content[3]) {  // 字符串小于
                printf("true\n");
            }
            else {
                printf("false\n");
            }
        }
        else if (content[2] == "-ge") {
            if (content[1] >= content[3]) { // 字符串大于等于
                printf("true\n");
            }
            else {
                printf("false\n");
            }
        }
        else if (content[2] == "-le") {
            if (content[1] <= content[3]) { // 字符串小于等于
                printf("true\n");
            }
            else {
                printf("false\n");
            }
        }
        else {  // 操作符不合法
            printf("test: %s: unary operator expected\n", content[2].c_str());  
        }
    }
    else if (content.size() == 3) { // 比较字符串或数字
        if (content[1] == "-z") {   // 判断字符串是否为空
            if (content[2] == "") {
                printf("true\n");
            }
            else {
                printf("false\n");
            }
        }
        else if (content[1] == "-n") {  // 判断字符串是否非空
            if (content[2] != "") {
                printf("true\n");
            }
            else {
                printf("false\n");
            }
        }
        else {  // 操作符不合法
            printf("test: %s: unary operator expected\n", content[1].c_str());
        }
    }
    else {  // 参数过多
        printf("test: too many arguments\n");
    }
}


// 置空环境变量
void UNSET(vector<string> content) {
    if (content.size() == 1) {  // 无参时报错
        printf("unset: not enough arguments\n");
    }
    else {  // 带参时置空环境变量
        for (int i = 1; i < content.size(); i++) {
            unsetenv(content[i].c_str());
        }
    }
}
```

signal_handler.h
```c++
#pragma once

#include "myshell.h"

using namespace std;

void signal_init();
void HANDLER_STOP(int sign);
void HANDLER_SIGCHLD(int sign, siginfo_t* sign_info, void* context);


void signal_init() {    
    signal(SIGTSTP, HANDLER_STOP);  // 处理ctrl z信号
    signal(SIGSTOP, HANDLER_STOP);  
    memset(&new_act, 0, sizeof(new_act));   
    new_act.sa_sigaction = HANDLER_SIGCHLD; // 处理子进程结束信号
    new_act.sa_flags = SA_RESTART | SA_SIGINFO; // 重新执行被信号中断的系统调用
    sigemptyset(&new_act.sa_mask);  // 清空信号集
    sigaction(SIGCHLD, &new_act, &old_act); // 注册信号处理函数
}


void HANDLER_STOP(int sign) {
    // if (sign != SIGTSTP && sign != SIGSTOP)
    //     return;
    // cout << "test: ctrl z" << endl;
    // exit(0);
    // cout << jobs[jobs_info->total_jobs - 1].pid << " " << jobs[jobs_info->total_jobs - 1].is_running << " " ;
    // cout << jobs[jobs_info->total_jobs - 1].is_FG << endl;
    if (jobs[jobs_info->total_jobs - 1].pid != 0 && jobs[jobs_info->total_jobs - 1].is_running  && jobs[jobs_info->total_jobs - 1].is_FG) { // foreground process
        // cout << "test: ctrl z_in" << endl;
        kill(jobs[jobs_info->total_jobs - 1].pid, SIGSTOP); // 停止进程
        jobs[jobs_info->total_jobs - 1].is_FG = false;      // 标记为后台进程
        jobs[jobs_info->total_jobs - 1].is_running = false; // 标记为未运行
        jobs[jobs_info->total_jobs - 1].end_of_run = false; // 标记为未结束

        int job_index = jobs_info->total_jobs - 1;        // 获取当前job的索引
        jobs[job_index].bg_index = (++jobs_info->bg_jobs);  // 设置后台进程的索引
        cout << "[" << jobs[job_index].bg_index << "] " << jobs[job_index].pid << "    Stopped    " << jobs[job_index].job_name << endl; // 输出信息
    } else {
        cout << endl;   // 输出空行
    }
}


void HANDLER_SIGCHLD(int sign, siginfo_t* sign_info, void* context) { 
    // cout << "handler: SIGCHLD " << sign_info->si_pid << endl;
    if (BG_flag) { // background process
        BG_flag = 0;
        return;
    }
    int i = find_job(sign_info->si_pid);
    if (i != -1) { 
        //cout << "1" << endl;
        if (!jobs[i].is_FG && jobs[i].is_running) { // 后台进程结束
            //cout << "2: "<< i << " " << jobs[i].job_name << endl;
            //jobs[i].is_running = false;
            jobs[i].end_of_run = true;  // 标记为已结束
            // cout << "[" << jobs[i].pid << "]" << "    Done    " << jobs[i].job_name << endl;
            // cout << "job_i " << i << endl; 
            // kill(jobs[i].pid, SIGKILL);
            //delete_job(jobs[i].pid);
        }
        else if (!jobs[i].is_FG && !jobs[i].is_running) {   // 已经结束的后台进程
            //cout << "3" << endl;
        }
        else {
            //cout << "4" << endl;
            delete_job(jobs[i].pid);
        }
    } else {
        // cout << "error" << endl;
    }
}
```



**心得体会**：

本次实验由七个小实验组成，总体难度相较于实验一，有明显的提升。前面两个实验关于使用makefile编译，相对简单。后续几个实验围绕shell开展编程，虽然在实验一中我掌握了基本的shell命令，也学习了课本第二章shell编程的内容，但是在具体上手写代码的时候还是遇到了不少的麻烦，很多时候会忘记语法。
最后一个实验要求实现一个简易的shell。实验的难点在于框架的搭建，需要弄清楚前台后台多进程运行的原理。在刚开始的时候我几乎找不到任何地方下手，后来翻看课本第六章和第七章有关进程控制和通信的内容，思路才逐渐清晰起来。
这是我第一次写多进程的程序，在调试的时候我也遇到了麻烦。我在使用vscode的调试工具进行调试时，无论如何配置，调试时总是会自动进入父进程。后来我学习了课本中GDB的使用方式，并在网上找到了可以在调试过程中随时切换到任意进程的方法。这次试验间接地促使我去在终端中使用GDB，体会到了该工具的强大功能（vscode调试工具内核也是GDB，但应该省去了不少交互的功能）。
总的来说，在该课程的学习中我对linux系统的操作有了基本认知，熟悉了shell的运行原理并掌握了shell的操作方法，此外，对C/C++程序的编译的详细流程有了深入的认识。以上这些能力为将来我成为一个优秀的程序员打下非常好的基础。
