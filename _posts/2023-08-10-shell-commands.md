---
layout: post
title: notes for shell command in linux
date: 2023-08-10 23:18 +0800
last_modified_at: 2023-8-20 01:08:25 +0800
tags: [report, code, linux]
toc:  true
---

<font face = "宋体">

<center>

# 实验1 linux shell 基本命令

林恒旭 3210105632

</center>

<br/>
<br/>

## 一、实验目的
1.	初步了解Linux的命令格式；
2.	学会如何得到帮助信息；
3.	练习几个最常用的命令；
4.	练习用vi/vim编辑器编辑文本文件；
5.	学习掌握Linux文件类型概念
6.	学习如何创建一个Linux目录的层次结构
7.	学习掌握有关绝对路径和相对路径概念，掌握主目录(home directory)、工作目录（当前目录）概念
8.	学习如何有效浏览Linux目录层次，有关文件内容类型和隐含文件
9.	学习有关文件属性，如何确定文件的大小
10.	学习如何显示文本文件的内容
11.	学习如何复制、追加、移动和删除文件，如何合并文件
12.	学习Linux的文件访问权限，用户的类型和文件访问权限的类型
13.	学习如何设置和改变一个文件的访问权限
14.	学习如何在文件或目录的创建时设置缺省访问权限
15.	学习理解硬链接、符号链接
16.	学习了解Linux进程的属性
17.	学习理解Linux的前台进程、后台进程及守护进程
18.	学习理解Linux命令的顺序执行和并发执行
19.	学习使用Linux的I/O重定向操作和管道操作
20.	学习使用data、cal、pwd、w、who、uname、cat、nl、head、tail、ls、cp、wc、mkdir、cd、mv、find、grep等命令
21.	学习使用id、chmod、chgrp、chown、umask、ln、ps、kill、fg、bg、job、top、pstree、crontab、at、|、<、>等命令

<br/>
<br/>
<br/>
<br/>


## 二、实验环境
- 内核/操作系统/CPU信息：Linux t14 6.2.0-25-generic #25-Ubuntu SMP PREEMPT_DYNAMIC Fri Jun 16 17:05:07 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
- 操作系统版本：Ubuntu 23.04

## 三、实验内容

<br/>
<font face = "黑体">

1. *查阅资料，回答问题：什么是软件许可证（Software License）？通过互联网收集国际上常用的各种软件许可证，并作简单的讨论和分析？重点研究Linux的软件许可存在哪些法律风险和应用风险？*

</font>

软件许可证是一种法律协议，规定了软件开发者与最终用户之间关于软件使用的权利和限制。它们确定了用户可以如何使用软件、是否可以修改软件、是否可以重新分发软件以及软件开发者的责任和义务等方面的条款和条件。软件许可证的主要目的是保护软件的知识产权，并确保软件开发者与用户之间的合作关系是合法和透明的。

以下是一些常见的软件许可证：

- 专有许可证（Proprietary License）：这种许可证通常由软件公司授予，对软件的使用和分发有严格的限制。专有许可证通常不允许用户查看、修改或重新分发软件的源代码。

- 开源许可证（Open Source License）：开源许可证允许用户查看、修改和重新分发软件的源代码。开源许可证有不同的类型，如GNU通用公共许可证（GNU General Public License，GPL）、MIT许可证、Apache许可证等。

- 自由软件许可证（Free Software License）：自由软件许可证是一种开源许可证，它赋予用户使用、修改和重新分发软件的自由。自由软件许可证的目标是确保用户对软件的自由使用，并鼓励社区合作和共享。

对于Linux操作系统，它采用了开源模式，并使用了GNU通用公共许可证（GPL）作为其主要许可证。Linux内核以及许多与Linux相关的软件都是根据GPL发布的。这意味着用户可以自由地使用、修改和重新分发Linux内核和其他基于GPL的软件，但在重新分发时需要遵守一些特定的要求：

- 传播源代码：如果用户选择重新分发Linux内核或基于GPL的软件，他们必须同时提供相应的源代码，以便其他人可以查看和修改代码。

- 分发许可证：用户必须确保在重新分发时保持原始的GPL许可证条款，并向接收者提供相应的GPL许可证。

尽管Linux的开源许可证为用户提供了很大的自由度，但仍存在一些法律风险和应用风险：

- 法律风险：在重新分发Linux内核或基于GPL的软件时，必须确保遵守GPL的要求，包括提供源代码和相应的许可证。如果这些要求没有满足，可能会导致法律纠纷和侵权指控。

- 应用风险：尽管用户可以修改和重新分发

<br/>
<font face = "黑体">

2. *使用man和info命令来获得每个Linux命令的帮助手册，用man ls，man passwd，info pwd命令得到ls、passwd、pwd三个命令的帮助手册。也可以使用：命令名 --help格式来显示该命令的帮助信息，如who --help，试一下这些命令。*

</font>

- man ls命令：
```bash{.line-numbers}
LS(1)                      User Commands                     LS(1)

NAME
       ls - list directory contents

SYNOPSIS
       ls [OPTION]... [FILE]...

DESCRIPTION
       List  information about the FILEs (the current directory by
       default).  Sort entries alphabetically if none of -cftuvSUX
       nor --sort is specified.

       Mandatory arguments to long options are mandatory for short
       options too.

       -a, --all
              do not ignore entries starting with .
```

- man passwd命令:
```bash{.line-numbers}
PASSWD(1)                    用户命令                    PASSWD(1)

名称
       passwd - 更改用户密码

大纲
       passwd [选项] [登录]

描述
       The passwd command changes passwords for user accounts. A
       normal user may only change the password for their own
       account, while the superuser may change the password for
       any account.  passwd also changes the account or associated
       password validity period.
```

- info pwd命令
```bash{.line-numbers}
Next: stty invocation,  Up: Working context

19.1 ‘pwd’: Print working directory
===================================

‘pwd’ prints the name of the current directory.  Synopsis:

     pwd [OPTION]...

   The program accepts the following options.  Also see *note Commo\
n
```

- who --help命令:
```bash{.line-numbers}
bill@t14:~$ who --help
用法：who [选项]... [ 文件 | 参数1 参数2 ]
显示当前已登录的用户信息。

  -a, --all         等价于 -b -d --login -p -r -t -T -u
  -b, --boot        显示上次系统引导时间
  -d, --dead        显示已死的进程
  -H, --heading     显示表头行
      --ips         print ips instead of hostnames. with --lookup,
                    canonicalizes based on stored IP, if available,
                    rather than stored hostname
  -l，--login       显示系统登录进程
      --lookup          尝试通过 DNS 规范化主机名
  -m                    只针对和标准输入有直接交互的主机和用户
```

<br/>
<font face = "黑体">

3. *使用whoami命令找到用户名。使用下面的命令显示有关你计算机系统信息：uname（显示操作系统的名称），uname -n（显示系统域名），uname -p（显示系统的CPU名称）*

	您的用户名是什么？
	你的操作系统名字是什么？
	你计算机系统的域名是什么？
	你计算机系统的CPU名字是什么？*

</font>

用户名：bill
操作系统名称；Linux
计算机系统域名：t14
计算机系统的CPU名字：x86_64

<br/>
<font face = "黑体">

4.  *简述Unix epoch（Unix纪元）？用cal命令显示下列年份的日历：4、1752、2012年7月。
(1)给出你显示以上年份年历的命令
(2)1752年有几天，为什么？*

</font>

- shell命令：
```shell{.line-numbers}
cal 7 4
cal 7 1752
cal 7 2012
```

- 1752年有355天。1752年的日历在9月份发生了历史上的调整。当时英国帝国采用了格里高利历（Gregorian calendar），取代了之前使用的朱利安历（Julian calendar）。为了调整两种历法之间的差异，9月份的日期从3日跳过了11天，变为9月14日。

<br/>
<font face = "黑体">

5.  *使用uptime命令判断系统已启动运行的时间和当前系统中有多少登录用户，给出显示的结果。*

</font>

显示结果：
```bash{.line-numebers}
 11:55:30 up 26 min, 2 users, load average: 0.20, 0.18, 0.12
```
- 11:50:30 : 当前时间
- 26min : 系统已经运行了26分钟
- load average: 0.20, 0.18, 0.12 : 系统的负载平均值，分别表示最近1分钟、5分钟和15分钟的平均负载情况。

<br/>
<font face = "黑体">

6. *通过Linux的man、info命令或互联网得到下面的shell命令、系统调用和库函数功能描述及每个命令使用例子：*

</font>

| 命令 | 命令功能的简要描述 | 实例 | 
| :---: | --- | --- |
| touch | 创建一个空的文件或更新现有文件的访问和修改时间戳 | touch file |
| cp | 将文件或目录复制到目标位置 | cp file des/ |
| mv | 移动文件或目录到目标位置，或者在重命名文件或目录时使用 | mv file1 file2 |
| rm | 删除文件或目录 | rm file |
| mkdir | 创建一个新目录 | mkdir newdir |
| who | 显示当前登录到系统的用户信息 | who |
| ls | 列出目录中的文件和子目录 | ls -l |
| cd | 更改当前工作目录 | cd home |
| pwd | 显示当前工作目录的路径 | pwd |
| open | 用适当的应用程序打开指定的文件或目录 | open file |
| read | 从标准输入或文件中读取数据 | read -a array | 
| write | 将数据写入标准输出或文件中 | write(fd,buf,strlen(buf)) |
| close | 关闭文件描述符或打开的文件 | close(fd) |
| pipe | 创建一个管道，用于进程间通信 | command1 \| command2 |
| socket | 创建一个网络套接字，用于网络通信 | int sockfd = socket(AF_INET, SOCK_STREAM, 0); |
| mkfifo | 创建一个命名管道（FIFO），用于进程间通信 | mkfifo myfifo |
| system | 执行一个系统命令 | system("ls -l"); |
| printf | 根据指定的格式字符串打印格式化输出 | printf("a"); |

<br/>
<font face = "黑体">

7. *vi/vim编辑器的使用：
(1)	在shell提示符下，输入vim firscrip并按\<Enter>键。vim的界面将出现在显示屏上；
(2)	按\<A>健，输入ls  –la，并按\<Enter>键；``21	ASDFD	2
(3)	输入who，并按\<Enter>键；
(4)	输入pwd，再按\<Enter>键。
(5)	在按\<Esc>键；
(6)	再输入:wq，并按\<Enter>键；
(7)	在shell提示符下，输入bash  ./firscrip并按\<Enter>键；
(8)	观察结果。当前的工作目录中有多少个文件？他们的名称和大小？还有谁在使用你的计算机系统？当前的工作目录是什么？*

</font>

```bash{.line-numbers}
bill@t14:~/Documents$ bash ./firscrip
总计 60
drwxr-xr-x  3 bill bill  4096  7月  8 20:58 .
drwxr-x--- 18 bill bill  4096  7月  8 21:09 ..
-rw-rw-r--  1 bill bill    16  7月  6 20:29 firscrip
-rwxrwx-wx  2 root root  2318  7月  8 19:52 record.txt
-rwxrwx-wx  2 root root  2318  7月  8 19:52 record.txt.hard
-rw-rw-r--  1 bill bill     0  7月  8 17:10 test1.html
-rw-rw-r--  1 bill bill    43  7月  8 19:08 test1.txt
-rw-rw-r--  1 bill bill     0  7月  8 17:11 test2.htm
-rw-rw-r--  1 bill bill     0  7月  5 09:28 test2.txt
-rw-rw-r--  1 bill bill     0  7月  8 17:11 test3.c
drwxrwxr-x  2 bill bill  4096  7月  8 16:33 testdir
-rwxrwxr-x  1 bill bill 16520  7月  6 16:19 testp
-rw-rw-r--  1 bill bill    91  7月  6 16:22 testp1
-rw-rw-r--  1 bill bill    95  7月  6 16:23 testp.cpp
-rw-rw-r--  1 bill bill    13  7月  5 09:11 test.txt
bill     seat0        2023-07-08 13:40 (login screen)
bill     tty2         2023-07-08 13:40 (tty2)
bill     pts/1        2023-07-08 13:56 (192.168.216.1)
bill     pts/2        2023-07-08 19:09 (192.168.216.1)
bill     pts/3        2023-07-08 19:44 (192.168.216.1)
bill     pts/4        2023-07-08 19:45

```
- 当前的工作目录共有10个文件，名称和大小见会话。
- 由图可知用户'bill'在使用计算机。

<br/>
<font face = "黑体">

8.	*在你的主目录下建立如下图所示的目录树。“Your Home Directory”表示你的主目录，不需要再建立。给出完成这项工作的所有会话。（会话是指你命令的输入和结果的输出，你提交的作业应包含这些内容）*

</font>

目录树创建会话如下：
```bash{.line-numbers}
root@t14:/home# mkdir -pv -m 777 temp
mkdir: 已创建目录 'temp'
root@t14:/home# mkdir -pv ./professional/courses/major/cs213
mkdir: 已创建目录 './professional'
mkdir: 已创建目录 './professional/courses'
mkdir: 已创建目录 './professional/courses/major'
mkdir: 已创建目录 './professional/courses/major/cs213'
root@t14:/home# mkdir -pv ./professional/courses/major/cs381/notes
mkdir: 已创建目录 './professional/courses/major/cs381'
mkdir: 已创建目录 './professional/courses/major/cs381/notes'
root@t14:/home# mkdir -pv ./professional/courses/major/cs381/labs
mkdir: 已创建目录 './professional/courses/major/cs381/labs'
root@t14:/home# mkdir -pv ./professional/courses/major/cs381/programs
mkdir: 已创建目录 './professional/courses/major/cs381/programs'
root@t14:/home# mkdir -pv ./professional/courses/major/cs475
mkdir: 已创建目录 './professional/courses/major/cs475'
root@t14:/home# mkdir -pv ./professional/courses/general
mkdir: 已创建目录 './professional/courses/general'
root@t14:/home# mkdir -pv ./professional/societies/ieee
mkdir: 已创建目录 './professional/societies'
mkdir: 已创建目录 './professional/societies/ieee'
root@t14:/home# mkdir -pv ./professional/societies/acm
mkdir: 已创建目录 './professional/societies/acm'
root@t14:/home# mkdir -pv ./personal/funstuff
mkdir: 已创建目录 './personal'
mkdir: 已创建目录 './personal/funstuff'
root@t14:/home# mkdir -pv ./personal/taxes
mkdir: 已创建目录 './personal/taxes'
```

部分目录树展示：
```bash{.line-numbers}
├── professional
    │   ├── courses
    │   │   ├── general
    │   │   └── major
    │   │       ├── cs213
    │   │       ├── cs381
    │   │       │   ├── labs
    │   │       │   │   └── lab1
    │   │       │   ├── notes
    │   │       │   └── programs
    │   │       └── cs475
    │   └── societies
    │       ├── acm
    │       └── ieee
```


<br/>
<font face = "黑体">

9.	*在系统中，执行cd professional/courses 命令，回答下列问题：
(1)	你的主目录的绝对路径是什么？给出获得该绝对路径的命令及命令输出。
(2)	acm目录的绝对路径是什么？
(3)	给出acm目录的两个相对路径。
(4)	执行cd major/cs381/labs命令。然后执行一个命令显示当前目录的绝对路径，给出这个会话过程。
(5)	给出获得你的主目录三个不同的命令。*

</font>

- 主目录绝对路径：/home/bill
  ```bash{.line-numbers}
  pwd
  /home/bill
  ```
- acm目录的绝对路径：
  ```bash{.line-numbers}
  bill@t14:~$ sudo find /home/bill -name  acm
  /home/bill/professional/societies/acm
  ```
- acm相对路径：
  societies/acm
  professional/societies/acm
  
- 会话：
    ```bash{.line-numbers}
  bill@t14:~$ cd professional/courses/
  bill@t14:~/professional/courses$ cd major/cs381/labs/
  bill@t14:~/professional/courses/major/cs381/labs$ pwd
  /home/bill/professional/courses/major/cs381/labs
  ```
- 会话：
  ```bash{.line-numbers}
  bill@t14:~$ pwd
  /home/bill
  bill@t14:~$ echo $HOME
  /home/bill
  bill@t14:~$ eval echo "~$USER"
  /home/bill
  bill@t14:~$
  
  ```

<br/>
<font face = "黑体">

10.	 *Linux系统规定，隐含文件是首字符为“.”的文件，如.profile。在你的系统中的主目录下查找隐含文件，它们分别是哪些？*

</font>

以下列出了主目录中包括所有文件，以“.”开头的隐含文件。
```bash{.line-numbers}
bill@t14:~$ ls -a
.              Downloads        snap
..             .lesshst         .ssh
.bash_history  .local           .sudo_as_admin_successful
.bash_logout   Music            temp
.bashrc        personal         Templates
.bashrc.swp    Pictures         Videos
.cache         professional     .viminfo
.config        .profile         .Xauthority
Desktop        Public
Documents      .python_history
```

<br/>
<font face = "黑体">

11.	*下面这些目录的inode号是多少：/、你的主目录（home directory）、\~/temp、 \~/professional、和\~/personal? 写出会话过程。*

</font>

```bash{.line-numbers}
bill@t14:/$ ls -id /
2 /
bill@t14:/$ ls -id /home/bill
393218 /home/bill
bill@t14:/$ ls -id ~/temp
708050 /home/bill/temp
bill@t14:/$ ls -id ~/perfessional
ls: 无法访问 '/home/bill/perfessional': 没有那个文件或目录
bill@t14:/$ ls -id ~/professional
658603 /home/bill/professional
bill@t14:/$ ls -id ~/personal
708069 /home/bill/personal
bill@t14:/$
```

<br/>
<font face = "黑体">

12.	*在labs目录下，用文本编辑器创建一个名字为lab1的文件，文件的内容为：“Use a text editor to create a file called lab1 under the labs directory in your directory hierarchy. The file should contain the text of this problem. ”。回答下列问题：
(1)	lab1文件的类型，用Linux命令回答这个问题，给出会话过程。
(2)	lab1文件内容的类型，用Linux命令回答这个问题，给出会话过程。*

</font>

会话如下，lab1文件类型为普通文件，文件内容的类型为ASCII text。
```bash{.line-numbers}
bill@t14:~/professional/courses/major/cs381/labs$ ls -l
总计 4
-rw-rw-r-- 1 bill bill 75  7月  8 15:44 lab1
bill@t14:~/professional/courses/major/cs381/labs$ file lab1
lab1: ASCII text
```

<br/>
<font face = "黑体">

13.	*在linux系统中，头文件以.h为扩展名。在/usr/include目录中，显示所有以t字母开头的头文件的名字。给出会话过程。*

</font>

```bash{.line-numbers}
bill@t14:/usr/include$ ls -ltr t*
-rw-r--r-- 1 root root  2494  3月 16 16:44 ttyent.h
-rw-r--r-- 1 root root 15091  3月 16 16:44 time.h
-rw-r--r-- 1 root root  7798  3月 16 16:44 threads.h
-rw-r--r-- 1 root root 16024  3月 16 16:44 thread_db.h
-rw-r--r-- 1 root root 47626  3月 16 16:44 tgmath.h
-rw-r--r-- 1 root root  3599  3月 16 16:44 termios.h
-rw-r--r-- 1 root root   214  3月 16 16:44 termio.h
-rw-r--r-- 1 root root  3752  3月 16 16:44 tar.h

tirpc:
总计 12
-rw-r--r-- 1 root root 2195  8月 11  2022 netconfig.h
drwxr-xr-x 2 root root 4096  7月  4 17:52 rpcsvc
drwxr-xr-x 2 root root 4096  7月  4 17:52 rpc
```

<br/>
<br/>
<font face = "黑体">

14.	*创建几个大小不等的文本文件，供本实验和后面几个实验用：用man cat > mediumFile 命令创建中等大小的文件；用man bash >largeFile 命令创建一个大文件；再创建一个名字为smallFile关于学生数据的小文件，文件每行内容如下，第一行为各自段的含义，注意字段之间用tab符隔开：
FirstName	LastName	Major	GPA	Email	           Phone
John	Doe	ECE	3.54	doe@jd.home.org	111.222.3333
James	Davis	ECE	3.71	davis@jd.work.org	111.222.1111
Al	Davis	CS	2.63	davis@a.lakers.org	111.222.2222
Ahmad	Rashid	MBA	3.04	ahmad@mba.org	111.222.4444
Sam	Chu	ECE	3.68	chu@sam.ab.com	111.222.5555
Arun	Roy	SS	3.86	roy@ss.arts.edu	111.222.8888
Rick	Marsh	CS	2.34	marsh@a.b.org	111.222.6666
James	Adam	CS	2.77	jadam@a.b.org	111.222.7777
Art	Pohm	ECE	4.00	pohm@ap.a.org	111.222.9999
John	Clark	ECE	2.68	clark@xyz.ab.com	111.111.5555
Nabeel	Ali	EE	3.56	ali@ee.eng.edu	111.111.8888
Tom	Nelson	ECE	3.81	nelson@tn.abc.org	111.111.6666
Pat	King	SS	3.77	king@pk.xyz.org	111.111.7777
Jake	Zulu	CS	3.00	zulu@jz.sa.org	111.111.9999
John	Lee	EE	3.64	jlee@j.lee.com	111.111.2222
Sunil	Raj	ECE	3.86	raj@sr.cs.edu	111.111.3333
Charles	Right	EECS	3.31	right@cr.abc.edu	111.111.4444
Diane	Rover	ECE	3.87	rover@dr.xyz.edu	111.111.5555
Aziz	Inan	EECS	3.75	ainan@ai.abc.edu	111.111.1111*

<br/>
<br/>

15.	*显示largeFile文件的开始12行内容，显示smallFile文件的最后5行内容，要用什么命令？使用命令 tail –n +6 smallFile显示smallFile文件从第6行开始到结束全部行？给出你的会话。*

</font>

```bash{.line-numbers}
bill@t14:~/Documents/testdir$ head -12 largeFile
BASH(1)               General Commands Manual              BASH(1)

NAME
       bash - GNU Bourne-Again SHell

SYNOPSIS
       bash [options] [command_string | file]

COPYRIGHT
       Bash  is Copyright (C) 1989-2022 by the Free Software Foun‐
       dation, Inc.

bill@t14:~/Documents/testdir$ tail -5 smallFile
John            Lee             EE      3.64    jlee@j.lee.com     111.111.2222
Sunil           Raj             ECE     3.86    raj@sr.cs.edu      111.111.3333
Charles         Right           EECS    3.31    right@cr.abc.edu   111.111.4444
Diane           Rover           ECE     3.87    rover@dr.xyz.edu   111.111.5555
Aziz            Inan            EECS    3.75    ainan@ai.abc.edu   111.111.1111
bill@t14:~/Documents/testdir$ tail -n +6 smallFile
Sam             Chu             ECE     3.68    chu@sam.ab.com     111.222.5555
Arun            Roy             SS      3.86    roy@ss.arts.edu    111.222.8888
Rick            Marsh           CS      2.34    marsh@a.b.org      111.222.6666
James           Adam            CS      2.77    jadam@a.b.org      111.222.7777
Art             Pohm            ECE     4.00    pohm@ap.a.org      111.222.9999
John            Clark           ECE     2.68    clark@xyz.ab.com   111.111.5555
Nabeel          Ali             EE      3.56    ali@ee.eng.edu     111.111.8888
Tom             Nelson          ECE     3.81    nelson@tn.abc.org  111.111.6666
Pat             King            SS      3.77    king@pk.xyz.org    111.111.7777
Jake            Zulu            CS      3.00    zulu@jz.sa.org     111.111.9999
John            Lee             EE      3.64    jlee@j.lee.com     111.111.2222
Sunil           Raj             ECE     3.86    raj@sr.cs.edu      111.111.3333
Charles         Right           EECS    3.31    right@cr.abc.edu   111.111.4444
Diane           Rover           ECE     3.87    rover@dr.xyz.edu   111.111.5555
Aziz            Inan            EECS    3.75    ainan@ai.abc.edu   111.111.1111
```

<br/>
<font face = "黑体">

16.	*复制smallFile文件的拷贝，文件名为dataFile。用ls –l命令观察这两个文件的修改时间是否一样。它们是不同的，dataFile文件的修改时间应该是这个文件的创建时间。什么命令能够保留这个修改时间不变呢？这两个文件的inode号是多少？
再把文件名dataFile 改成（移动）newDataFile，文件newDataFile的inode多少？与dataFile文件的inode号是否相同，若相同，为什么？
然后再把文件newDataFile移动到/tmp目录下，文件/tmp/newDataFile的inode号是多少？比较结果如何，为什么？
给出完成上述工作的会话过程。*

</font>

会话过程如下：
```bash{.line-numbers}
bill@t14:~/Documents/testdir$ cp smallFile dataFile
bill@t14:~/Documents/testdir$ ls -l
总计 424
-rw-rw-r-- 1 bill bill   1027  7月  8 16:28 dataFile
-rw-rw-r-- 1 bill bill 418354  7月  8 16:06 largeFile
-rw-rw-r-- 1 bill bill   2024  7月  8 16:06 mediumFile
-rw-rw-r-- 1 bill bill   1027  7月  8 16:13 smallFile
bill@t14:~/Documents/testdir$ cp -p smallFile dataFile1
bill@t14:~/Documents/testdir$ ls -l
总计 428
-rw-rw-r-- 1 bill bill   1027  7月  8 16:28 dataFile
-rw-rw-r-- 1 bill bill   1027  7月  8 16:13 dataFile1
-rw-rw-r-- 1 bill bill 418354  7月  8 16:06 largeFile
-rw-rw-r-- 1 bill bill   2024  7月  8 16:06 mediumFile
-rw-rw-r-- 1 bill bill   1027  7月  8 16:13 smallFile
bill@t14:~/Documents/testdir$ ls ./* -id
797097 ./dataFile   797094 ./largeFile   797095 ./smallFile
797098 ./dataFile1  786913 ./mediumFile
bill@t14:~/Documents/testdir$ mv dataFile1 newdataFile
bill@t14:~/Documents/testdir$ ls ./* -id
797097 ./dataFile   786913 ./mediumFile   797095 ./smallFile
797094 ./largeFile  797098 ./newdataFile
bill@t14:~/Documents/testdir$ mv newdataFile /tmp
bill@t14:~/Documents/testdir$ ls /tmp/newdataFile -id
797098 /tmp/newdataFile
```
- 使用`cp -p smallFile dataFile1`命令，可以保持原先文件的所有者，组权限和时间标志。从会话中可以看到，这两个文件的节点号分别为797095和797098。
- 移动或重命名文件不会修改节点号。因为节点号是文件系统中用于唯一标识文件的标识符，它与文件的位置和内容无关，仅与文件的元数据（如权限、所有者、时间戳等）相关。

<br/>
<font face = "黑体">


17.	*在屏幕上显示文件smallFile、mediumFile、largeFile和/tmp/newDataFile的字节数、字数和行数。smallFile和/tmp/newDataFile文件应该是相同的。你能用其它命令给出这些文件的字节数的大小吗？什么命令。给出会话过程。*

</font>

会话如下，可以用wc命令显示文件的字节数、字数和行数。也可以用ls -l命令查看文件的字节数。
```bash{.line-numbers}
bill@t14:~/Documents/testdir$ wc smallFile mediumFile largeFile /tmp/newdataFile
    20    120   1027 smallFile
    77    219   2024 mediumFile
  7765  52809 418354 largeFile
    20    120   1027 /tmp/newdataFile
  7882  53268 422432 总计
bill@t14:~/Documents/testdir$ ls -sl
总计 424
  4 -rw-rw-r-- 1 bill bill   1027  7月  8 16:28 dataFile
412 -rw-rw-r-- 1 bill bill 418354  7月  8 16:06 largeFile
  4 -rw-rw-r-- 1 bill bill   2024  7月  8 16:06 mediumFile
  4 -rw-rw-r-- 1 bill bill   1027  7月  8 16:13 smallFile
```

<br/>
<font face = "黑体">

18.	*搜索你的主目录，找到所有的HTML和C程序文件（文件有.html、.htm或.c扩展名。如果没有这类文件，可以用touch等命令生成一些这样的文件），显示符合要求的文件路径和文件名。给出你的会话。*

</font>

```bash{.line-numbers}
bill@t14:~/Documents$ touch test1.html
bill@t14:~/Documents$ touch test2.htm
bill@t14:~/Documents$ touch test3.c
bill@t14:~/Documents$ sudo find /home/bill \( -name "*.html" -o -name "*.htm" -o -name "*.c" \) -print
/home/bill/Documents/test1.html
/home/bill/Documents/test3.c
/home/bill/Documents/test2.htm
```

<br/>
<font face = "黑体">

19.	*给出一条命令，在主目录下显示所有文件内容中包含字符串“Linux”的文件名。*

</font>

会话如下，使用`grep -l -r Linux /home/bill`命令，递归地在主目录/home/bill下查找内容中包含Linux字符的文件，从`tail -3 test1.txt`命令中看到text1.txt文件中含有目标字符串，同时该文件名也在grep命令的结果中给出（第五行）。
```bash{.line-numbers}
bill@t14:~$ grep -l -r Linux /home/bill
/home/bill/.cache/tracker3/files/http%3A%2F%2Ftracker.api.gnome.org%2Fontology%2Fv3%2Ftracker%23Documents.db-wal
/home/bill/.cache/gstreamer-1.0/registry.x86_64.bin
/home/bill/.bash_history
/home/bill/Documents/test1.txt
/home/bill/snap/firefox/common/.cache/mozilla/firefox/tohlm49m.default/startupCache/scriptCache-current.bin
/home/bill/snap/firefox/common/.cache/mozilla/firefox/tohlm49m.default/cache2/entries/AD40064F5F4FF144C4FAA64CFE407AB3C59CA998
/home/bill/snap/firefox/common/.cache/mozilla/firefox/tohlm49m.default/cache2/entries/8D3005BC96E990962D1CCADA5C67BCFEB300BC1B
/home/bill/snap/firefox/common/.cache/mozilla/firefox/tohlm49m.default/cache2/entries/1DC129F5146630DA594B50EC757BC9C4162FB633
/home/bill/snap/firefox/common/.cache/mozilla/firefox/tohlm49m.default/cache2/entries/27CCBD76EFB87DBD96A49A20A760A664D6C7B6C8
/home/bill/snap/firefox/common/.cache/mozilla/firefox/tohlm49m.default/cache2/entries/764010DDDA5F05BEFD5C8BBD8135CDFE4A722489
/home/bill/snap/firefox/common/.cache/mozilla/firefox/tohlm49m.default/cache2/entries/FF516FD002B9FCB4DC5BAB352A361FF72BDBCA7B
/home/bill/snap/firefox/common/.cache/mozilla/firefox/tohlm49m.default/cache2/entries/42AE3C7C6A10C10E28B927380071765AFC4DEADD
/home/bill/snap/firefox/common/.mozilla/firefox/tohlm49m.default/compatibility.ini
/home/bill/snap/firefox/common/.mozilla/firefox/tohlm49m.default/storage/permanent/chrome/idb/3870112724rsegmnoittet-es.sqlite
/home/bill/snap/firefox/common/.mozilla/firefox/tohlm49m.default/datareporting/glean/db/data.safe.bin
/home/bill/snap/firefox/common/.mozilla/firefox/tohlm49m.default/datareporting/glean/pending_pings/3a1758cf-ba12-4042-9c4d-4ac07b7b1fb7
/home/bill/snap/firefox/common/.mozilla/firefox/tohlm49m.default/datareporting/archived/2023-07/1688634554067.879e0c53-fa2e-4db9-bcf6-9c50dbeedb3a.main.jsonlz4
/home/bill/snap/firefox/common/.mozilla/firefox/tohlm49m.default/datareporting/archived/2023-07/1688634554070.e7f7ae43-7e51-43c5-bf50-73fd862bd292.first-shutdown.jsonlz4
/home/bill/snap/firefox/common/.mozilla/firefox/tohlm49m.default/datareporting/archived/2023-07/1688642667142.d2d7e9f1-2518-4857-b0f4-bbb998c2bc34.main.jsonlz4
/home/bill/snap/firefox/common/.mozilla/firefox/tohlm49m.default/datareporting/archived/2023-07/1688649052046.3dfc88ce-b49b-4b8b-913e-99a7ad5e1e59.main.jsonlz4
bill@t14:~/Documents$ tail -3 test1.txt
fsad
Linux

```

<br/>
<font face = "黑体">

20.	*在你的系统中有文件或目录分别是：/ 、 /etc/passwd 、/bin/df 、~ 。用长列表格式显示这些文件或目录，并填写下列表格。*

</font>

会话如下，这里使用option "-ld"查看目录本身的信息。
```bash{.line-numbers}
bill@t14:~/Documents$ sudo ls -ld /
drwxr-xr-x 19 root root 4096  7月  8 13:21 /
bill@t14:~/Documents$ sudo ls -l /etc/passwd
-rw-r--r-- 1 root root 2929  7月  4 17:44 /etc/passwd
bill@t14:~/Documents$ sudo ls -l /bin/df
-rwxr-xr-x 1 root root 89168  1月 10 21:19 /bin/df
bill@t14:~/Documents$ sudo ls -ld ~
drwxr-x--- 18 bill bill 4096  7月  8 14:33 /home/bill
```

<br/>

| 文件 | 文件类型 | 存取权限 | 链接数 | 所有者 | 组 | 文件大小(bytes) |
| :---: | --- | --- | --- | --- | --- | --- |
|   / | 目录 | rwxr-xr-x | 19 | root | root | 4096 |
|  /etc/passwd | 普通文件 | -rw-r--r-- | 1 | root | root | 2929 |
| /bin/df | 普通文件 | -rwxr-xr-x | 1 | root | root | 89168 | 
| ~ | 目录 | drwxr-x--- | 18 | bill | bill | 4096 | 

<br/>
<font face = "黑体">

21.	*在你的主目录中的temp、professional和personal三个子目录，设置使自己（owner）拥有读、写、执行3种访问权限，设置其它用户只有读和执行权限。在~/temp目录下创建名为d1、d2和d3的目录。在d1目录下，用touch命令创建一个名为f1的空文件。给出d1、d2、d3和f1的访问权限。给出完成这些工作的会话。*

</font>

```bash{.line-numbers}
bill@t14:~$ sudo chmod 755 personal
bill@t14:~$ sudo chmod 755 professional
bill@t14:~$ sudo chmod 755 temp
bill@t14:~$ sudo su
root@t14:/home/bill# cd temp
root@t14:/home/bill/temp# mkdir d1
root@t14:/home/bill/temp# mkdir d2
root@t14:/home/bill/temp# mkdir d3
root@t14:/home/bill/temp# cd d1
root@t14:/home/bill/temp/d1# touch f1
root@t14:/home/bill/temp/d1# cd ..
root@t14:/home/bill/temp# ls -R -l
.:
总计 12
drwxr-xr-x 2 root root 4096  7月  8 19:10 d1
drwxr-xr-x 2 root root 4096  7月  8 19:10 d2
drwxr-xr-x 2 root root 4096  7月  8 19:10 d3

./d1:
总计 0
-rw-r--r-- 1 root root 0  7月  8 19:10 f1

./d2:
总计 0

./d3:
总计 0
```

<br/>
<br/>

<font face = "黑体">

22.	*设置当前目录为你的主目录， 设置文件~/temp仅为执行权限， 然后执行ls -ld temp，再执行ls -l temp命令。结果如何？成功执行ls -l temp命令需要的最小权限是什么？请设置temp目录的最小权限，然后再一次执行ls -l temp命令。给出这个过程的会话。注意：做这个实验不能使用root用户登录系统。*

</font>

会话如下，说明temp目录最小权限为rwxrwxr-x时，可以执行ls -l temp命令。
```bash{.line-numbers}
bill@t14:~$ sudo chmod 711 temp
[sudo] bill 的密码：
bill@t14:~$ ls -l temp
ls: 无法打开目录 'temp': 权限不够
bill@t14:~$ ls -ld temp
drwx--x--x 5 root root 4096  7月  8 19:10 temp
bill@t14:~$ sudo chmod 774 temp
bill@t14:~$ ls -l temp
ls: 无法访问 'temp/d1': 权限不够
ls: 无法访问 'temp/d2': 权限不够
ls: 无法访问 'temp/d3': 权限不够
总计 0
d????????? ? ? ? ?             ? d1
d????????? ? ? ? ?             ? d2
d????????? ? ? ? ?             ? d3
bill@t14:~$ sudo chmod 775 temp
bill@t14:~$ ls -l temp
总计 12
drwxr-xr-x 2 root root 4096  7月  8 19:10 d1
drwxr-xr-x 2 root root 4096  7月  8 19:10 d2
drwxr-xr-x 2 root root 4096  7月  8 19:10 d3
```

<br/>
<font face = "黑体">

23.	*用umask命令显示当前的掩码。把你的主目录设置为当前目录，然后在~/temp/d1目录下，创建d11目录，用touch命令创建f2空文件。在temp目录下用编辑器创建hello.c文件，该文件的内容如下：*
    ```c{.line-numbers}
    #include <stdio.h>
    main(void)
    {
    printf (“Hello, world!\n”);
    }
    ```
    *再运行命令gcc –o greeting hello.c ，生成了可执行文件greeting。长列表显示f2、 hello.c、greeting和d1文件访问权限。*
    *把掩码（mask）设置为077，在目录~/temp/d2下，创建d21目录，用touch命令创建f2空文件。长列表显示f2、 hello.c、greeting和d21文件访问权限。最后根据掩码的不同填写下列表格。*

</font>

按照要求创建各个文件后，通过ls -R -l命令，列出需要的文件的访问权限。
```bash{.line-numbers}
root@t14:/home/bill/temp# umask
0022
root@t14:/home/bill/temp# ls -R -l
.:
总计 32
drwxr-xr-x 3 root root  4096  7月  8 19:38 d1
drwxr-xr-x 2 root root  4096  7月  8 19:10 d2
drwxr-xr-x 2 root root  4096  7月  8 19:10 d3
-rwxr-xr-x 1 root root 15952  7月  8 19:48 greeting
-rw-r--r-- 1 root root    61  7月  8 19:48 hello.c

./d1:
总计 4
drwxrwxrwx 2 root root 4096  7月  8 19:41 d11
-rw-r--r-- 1 root root    0  7月  8 19:10 f1

./d1/d11:
总计 0
-rw-rw-r-- 1 bill bill 0  7月  8 19:41 f2

./d2:
总计 0

./d3:
总计 0
root@t14:/home/bill/temp# cd d2
root@t14:/home/bill/temp/d2# mkdir d21
root@t14:/home/bill/temp/d2# touch f2
root@t14:/home/bill/temp/d2# ls -R -l
.:
总计 4
drwx------ 2 root root 4096  7月 8 18:56 d21
-rw------- 1 root root    0  7月 8 18:56 f2
```

| umask值 | f2 | hello.c | greeting | d11/d21 |
| --- | --- | --- | --- | --- |
|0022|-rw-rw-r--|-rw-r--r--|-rwxr-xr-x| d11: drwxrwxrwx|
|0077|-rw-------|-rw-------|-rwx------| d21: drwx------|
如上图，当掩码为默认值为0022，此时d11文件的权限为drwxrwxrwx是因为创建时使用了chmod命令。而掩码改为077后，在目录d2下**重新创建**了文件四个文件：f2，hello.c，greeting，d21。在Linux系统中执行文件和目录的缺省权限是777，文本文件的缺省权限为666，故减去掩码后权限值分别为700和600，这与实验结果相同。

<br/>
<font face = "黑体">

24.	*在~/temp目录下创建名为d1、d2和d3的目录。把文件smallFile拷贝到d1目录下，长列表格式显示文件smallFile，显示的内容包括inode号、访问权限、硬链接数、文件大小。给出完成这些工作的会话。*

</font>

```bash{.line-numbers}
root@t14:/home/bill# cp Documents/testdir/smallFile temp/d1/
root@t14:/home/bill# cd temp/d1
root@t14:/home/bill/temp/d1# ls -l -i smallFile
658558 -rw-r--r-- 1 root root 1027  7月  8 20:03 smallFile
root@t14:/home/bill/temp/d1# stat smallFile
  文件：smallFile
  大小：1027            块：8          IO 块大小：4096   普通文件
设备：253,0     Inode: 658558      硬链接：1
权限：(0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
访问时间：2023-07-08 20:03:09.968921591 +0800
修改时间：2023-07-08 20:03:09.968921591 +0800
变更时间：2023-07-08 20:03:09.968921591 +0800
创建时间：2023-07-08 20:03:09.968921591 +0800

```

<br/>
<font face = "黑体">

25.	*在~/temp目录下，把当前目录改变成d2。创建一个名字为smallFile.hard硬链接到d1目录下的smallFile文件。长列表格式显示smallFile.hard文件，与smallFile文件的属性进行比较。你如何确定smallFile和smallFile.hard是同一文件的两个名字，是链接数吗？给出你的会话过程。*

</font>

会话如下，首先在~/temp目录下，将当前目录改变为d2，然后创建 smallFile的硬链接，通过比较smallFile和smallFile.hard的节点号，发现这是同一个文件的两个名字。
```bash{.line-numbers}
root@t14:/home/bill# mv temp d2
root@t14:/home/bill# cd d2
root@t14:/home/bill/d2# ln d1/smallFile smallFile.hard
root@t14:/home/bill/d2# ls -l -i smallFile.hard
658558 -rw-r--r-- 2 root root 1027  7月  8 20:03 smallFile.hard
root@t14:/home/bill/d2# ls -l -i d1/smallFile
658558 -rw-r--r-- 2 root root 1027  7月  8 20:03 d2/d1/smallFile
```

<br/>
<font face = "黑体">

1.  *使用硬链接文件smallFile.hard显示smallFile文件的内容。然后取消你本人对smallFile文件读（r）权限，再显示文件的内容，发生了什么？根据以上练习，你能推断出什么？对smallFile文件增加读权限，再一次显示文件内容，发生了什么？最后作一个smallFile文件的备份，并删除smallFile文件，用smallFile.hard显示smallFile文件内容，又发生了什么？请你解释一下练习过程中的现象。*

</font>

会话如下，首先使用硬链接文件smallFile.hard显示smallFile文件的内容，发现两者内容相同；取消本人对smallFile的文件读权限，然后尝试打开smallFile.hard，结果为权限不够，说硬链接只是文件的另一个名称，其保持了文件的权限信息；当再次赋予smallFile读权限时，可以读取硬链接文件的内容；删除smallFile文件后，smallFile.hard文件中的内容依然存在，因为被删除的仅仅是Inode Table中的一条目录项，磁盘中的相应内容未被删除，可以通过硬链接来获取。

```bash{.line-numbers}
root@t14:/home/bill/d2# head -2 smallFile.hard
FirstName       LastName        Major   GPA     Email              Phone
John            Doe             ECE     3.54    doe@jd.home.org    111.222.3333
James           Davis           ECE     3.71    davis@jd.work.org  111.222.1111
root@t14:/home/bill/d2# cd d1
root@t14:/home/bill/d2/d1# sudo chmod o-r smallFile
root@t14:/home/bill/d2/d1# su bill
bill@t14:~/d2/d1$ cat ../smallFile.hard
cat: ../smallFile: 权限不够
bill@t14:~/d2/d1$ sudo chmod o+r smallFile
[sudo] bill 的密码：
bill@t14:~/d2/d1$ head -2 ../smallFile.hard
FirstName       LastName        Major   GPA     Email              Phone
John            Doe             ECE     3.54    doe@jd.home.org    111.222.3333
bill@t14:~/d2/d1$ sudo cp smallFile smallFile_copy
bill@t14:~/d2/d1$ rm -i smallFile
bill@t14:~/d2/d1$ sudo rm smallFile
bill@t14:~/d2/d1$ cd ..
bill@t14:~/d2$ head -2 smallFile.hard
FirstName       LastName        Major   GPA     Email              Phone
John            Doe             ECE     3.54    doe@jd.home.org    111.222.3333
```

<br/>
<font face = "黑体">

27.	*恢复/temp/d1/smallFile文件。创建一个名字为~/temp/d2/smallFile.soft软链接到~/temp/d1/smallFile文件。长列表格式显示smallFile.soft文件，比较这两个文件的属性。你如何确定smallFile 和smallFile.soft是两个不同的文件？是这两个文件的大小吗？给出你的会话过程。*

</font>

会话如下，由节点号可发现，smallFile和smallFile.soft是两个不同的文件，smallFile.soft文件的大小较小，因为其仅储存了smallFile的路径等信息。
```bash{.line-numbers}
bill@t14:~/d2/d1$ sudo ln -s smallFile smallFile.soft
bill@t14:~/d2/d1$ ls -l -i
总计 12
664682 drwxrwxrwx 2 root root 4096  7月  8 19:41 d11
705522 -rw-r--r-- 1 root root    0  7月  8 19:10 f1
658588 -rw-r--r-- 1 root root 1027  7月  8 21:44 smallFile
658711 -rw-r--r-- 1 root root 1027  7月  8 20:44 smallFile_copy
708057 lrwxrwxrwx 1 root root    9  7月  8 21:47 smallFile.soft -> smallFile
```

<br/>
<font face = "黑体">

28.	*使用软链接文件smallFile.soft显示smallFile文件的内容。然后取消你本人对smallFile文件读（r）权限，再显示文件的内容，发生了什么？根据以上练习，你能推断出什么？对smallFile文件增加读权限，再一次显示文件内容，发生了什么？最后作一个smallFile文件的备份，并删除smallFile文件，用smallFile.soft显示smallFile文件内容，又发生了什么？请你解释一下练习过程中的现象。*

</font>

会话如下，取消本人对smallFile的读权限后，无法读取smallFile.soft的内容，软链接（符号链接）只是一个指向原始文件的符号链接文件，它本身不具有独立的权限。软链接只是一个引用，它会指向实际文件的路径和权限；删除smallFile后，尝试读取smallFile.soft的内容，结果为“没有那个文件或目录”。

```bash{.line-numbers}
bill@t14:~/d2/d1$ head -2 smallFile.soft
FirstName       LastName        Major   GPA     Email              Phone
John            Doe             ECE     3.54    doe@jd.home.org    111.222.3333
bill@t14:~/d2/d1$ sudo chmod o-r smallFile
bill@t14:~/d2/d1$ head -2 smallFile.soft
head: 无法以读模式打开 'smallFile.soft': 权限不够
bill@t14:~/d2/d1$ sudo chmod o+r smallFile
bill@t14:~/d2/d1$ head -2 smallFile.soft
FirstName       LastName        Major   GPA     Email              Phone
John            Doe             ECE     3.54    doe@jd.home.org    111.222.3333
bill@t14:~/d2/d1$ sudo cp smallFile smallFile_copy
bill@t14:~/d2/d1$ sudo rm smallFile
bill@t14:~/d2/d1$ ls
d11  f1  smallFile_copy  smallFile.soft
bill@t14:~/d2/d1$ head -2 smallFile.soft
head: 无法以读模式打开 'smallFile.soft': 没有那个文件或目录
```

<br/>
<font face = "黑体">

29.	*在你使用的Linux系统中，有多少进程在运行？进程init(或systemd)、bash、ps的PID是多少？init、bash和ps进程的父进程是哪一个？这些父进程的ID是什么？给出你得到这些信息的会话过程。*

</font>

会话如下，利用ps -A命令查看所有运行的进程，并由wc命令统计出一共有419个进程正在运行；通过grep命令查看进程init(或systemd)、bash、ps的PID和PPID；可以使用pstree命令查看进程的父进程，也可以根据该进程的PPID直接查找父进程。

```bash{.line-numbers}
bill@t14:~$ ps -A -F | head -3
UID          PID    PPID  C    SZ   RSS PSR STIME TTY          TIME CMD
root           1       0  0 42199 12524   0 7月08 ?       00:00:05 /sbin/init
root           2       0  0     0     0  13 7月08 ?       00:00:00 [kthreadd]
bill@t14:~$ ps -A -F | wc -l
419
bill@t14:~$ ps -A -F | grep systemd
root         608       1  0 12424 15132  12 7月08 ?       00:00:01 /lib/systemd/systemd-journald
root         675       1  0  7372  8960   3 7月08 ?       00:00:00 /lib/systemd/systemd-udevd
systemd+     885       1  0  4405  8704   1 7月08 ?       00:00:00 /lib/systemd/systemd-networkd
systemd+     934       1  0  4051  7040   1 7月08 ?       00:02:07 /lib/systemd/systemd-oomd
systemd+     940       1  0  5055 12160   5 7月08 ?       00:00:00 /lib/systemd/systemd-resolved
systemd+     942       1  0 22423  7168   9 7月08 ?       00:00:00 /lib/systemd/systemd-timesyncd
message+    1040       1  0  3008  6400   4 7月08 ?       00:00:02 @dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
root        1094       1  0  4215  8064   6 7月08 ?       00:00:00 /lib/systemd/systemd-logind
bill        1762       1  0  4905 11648   7 7月08 ?       00:00:03 /lib/systemd/systemd --user
bill        1785    1762  0  2796  6016   3 7月08 ?       00:00:03 /usr/bin/dbus-daemon --session --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
bill        1863    1762  0 130231 17664  5 7月08 ?       00:00:00 /usr/libexec/gnome-session-binary --systemd-service --session=ubuntu
bill        9716    9619  0  2396  2432  11 16:56 pts/1    00:00:00 grep --color=auto systemd
bill@t14:~$ ps -A -F | grep bash
bill        5997    5970  0  2919  5120  10 7月08 pts/0   00:00:00 bash
root        6277    6276  0  2653  4224   3 7月08 pts/4   00:00:00 bash
bill        7299    7297  0  3121  6016   9 7月08 pts/5   00:00:00 -bash
bill        9619    9618  0  2917  5248   0 16:40 pts/1    00:00:00 -bash
bill        9718    9619  0  2396  2432  15 16:57 pts/1    00:00:00 grep --color=auto bash
bill@t14:~$ ps -A -F | grep -w "ps"
bill        9721    9619  0  3651  5120   5 16:59 pts/1    00:00:00 ps -A -F
bill        9722    9619  0  2396  2432   9 16:59 pts/1    00:00:00 grep --color=auto -w ps
bill@t14:~$ ps -A -F | grep 9619
bill        9619    9618  0  2917  5248   0 16:40 pts/1    00:00:00 -bash
bill        9728    9619  0  3651  5120   4 17:01 pts/1    00:00:00 ps -A -F
bill        9729    9619  0  2396  2432   8 17:01 pts/1    00:00:00 grep --color=auto 9619
bill@t14:~$ pstree -p | grep -w "bash"
           |-sshd(2624)-+-sshd(7161)---sshd(7297)---bash(7299)
           |            |-sshd(9544)---sshd(9618)---bash(9619)-+-grep(9731)
           |               |-gnome-terminal-(5970)-+-bash(5997)---sudo(6274)---sudo(6275)---su(6276)---bash(6277)
bill@t14:~$ pstree -p | grep -w "systemd"
systemd(1)-+-ModemManager(1125)-+-{ModemManager}(1145)
           |-systemd(1762)-+-(sd-pam)(1763)
           |-systemd-journal(608)
           |-systemd-logind(1094)
           |-systemd-network(885)
           |-systemd-oomd(934)
           |-systemd-resolve(940)
           |-systemd-timesyn(942)---{systemd-timesyn}(999)
           |-systemd-udevd(675)
```

<br/>
<font face = "黑体">

30.	*linux系统中，进程可以在前台或后台运行。前台进程在运行结束前一直控制着终端。若干个命令用分号（；）分隔形成一个命令行，用圆括号把多个命令挂起来，他们就在一个进程里执行。使用“&”符作为命令分隔符，命令将并发执行。可以在命令行末尾加“&”使之成为后台命令。
请用一行命令实现以下功能：它1小时（实验中可以用1分钟代替）分钟后在屏幕上显示文字“Time for Lunch!”来提醒你去吃午餐。给出会话过程。*

</font>

```bash{.line-numbers}
bill@t14:~$ sleep 10 && echo "Time for Lunch!" &
[3] 10294
bill@t14:~$ Time for Lunch!
```

<br/>
<font face = "黑体">

31.  *写一命令行，使得date 、uname –a 、who 和ps并发执行。给出会话过程。*

</font>

会话如下，可以看到并发执行，并在终端上以随机顺序显示。

```bash{.line-numbers}
bill@t14:~$ date & uname -a & who & ps
[1] 10388
[2] 10389
[3] 10390
Linux t14 6.2.0-24-generic #24-Ubuntu SMP PREEMPT_DYNAMIC Fri Jun 16 12:03:50 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
2023年 07月 09日 星期日 17:40:30 CST
bill     seat0        2023-07-08 13:40 (login screen)
bill     tty2         2023-07-08 13:40 (tty2)
bill     pts/1        2023-07-09 16:40 (192.168.216.1)
bill     pts/4        2023-07-08 19:45
bill     pts/5        2023-07-08 21:42 (192.168.216.1)
    PID TTY          TIME CMD
   9619 pts/1    00:00:00 bash
  10391 pts/1    00:00:00 ps
[1]   已完成               date
[2]-  已完成               uname -a
[3]+  已完成               who
```

<br/>
<font face = "黑体">

32.	*写一命令行，先后执行date 、uname –a 、who 和ps命令，后面3个命令的执行条件是：当只有前面一个命令执行成功后，才能执行后面一个命令。给出会话过程。*

</font>

```bash{.line-numbers}
bill@t14:~$ date && uname -a && who && ps
2023年 07月 09日 星期日 17:44:07 CST
Linux t14 6.2.0-24-generic #24-Ubuntu SMP PREEMPT_DYNAMIC Fri Jun 16 12:03:50 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
bill     seat0        2023-07-08 13:40 (login screen)
bill     tty2         2023-07-08 13:40 (tty2)
bill     pts/1        2023-07-09 16:40 (192.168.216.1)
bill     pts/4        2023-07-08 19:45
bill     pts/5        2023-07-08 21:42 (192.168.216.1)
    PID TTY          TIME CMD
   9619 pts/1    00:00:00 bash
  10396 pts/1    00:00:00 ps
```

<br/>
<font face = "黑体">

33.	*在shell下执行下面的命令。3个pwd命令的运行结果是什么？。*

</font>


```bash{.line-numbers}
bill@t14:~$ pwd
/home/bill
bill@t14:~$ bash
bill@t14:~$ cd /usr
bill@t14:/usr$ pwd
/usr
bill@t14:/usr$
exit
bill@t14:~$ pwd
/home/bill
```

<br/>
<font face = "黑体">

34.	*搜索并显示你主目录下foobar文件的绝对路径，错误信息重定向到/dev/null中。给出你的会话。（提示：若没有foorbar文件，可以创建一个）*

</font>

```bash{.line-numbers}
bill@t14:~$ find ~ -name "foobar" 2>/dev/null
/home/bill/foobar
```

<br/>
<font face = "黑体">

35.	*搜索你主目录下foobar文件，保存它的绝对路径到foobar.path文件中，错误信息写到/dev/null中，再显示foobar.path文件的内容。给出会话过程。*

</font>

```bash{.line-numbers}
bill@t14:~$ find ~ -name "foobar" > Documents/foobar.path 2>/dev/null
bill@t14:~$ cat  Documents/foobar.path                    
/home/bill/Documents/foobar
```

<br/>
<font face = "黑体">

36.	*cat程序（命令），输入从标准输入设备中读入，输出送到标准输出设备中。现在运行这个程序，要求输入从student.records中读入，输出结果重定向到output.data文件中，错误重定向到error.log文件中。用一条命令来实现上述过程。*

</font>

```bash{.line-numbers}
root@t14:/home/bill/Documents/testdir# cat < student.records > output.data 2>error.log
```

<br/>
<font face = "黑体">

37.	*写出一个命令将shell的标准输入（stdin）更改到当前目录下名为fdata文件，标准输出（stdout）更改到当前目录下名为fout的文件。如果fdata文件包含下面的内容，那么在命令执行后会发生什么？
    echo –n “The time now is:”
    date
    echo –n “The users presently logged on are:”
    who
    提示：使用exec命令*

</font>

会话如下：命令 `exec < fdata > fout` 把这个进程的标准输入重定向到fdata，并把标准输出重定向到fout。执行该命令后查看fout文件的内容，可以看到fout文件中记录了时间和用户信息。
```bash{.line-numbers}
root@t14:/home/bill/Documents/testdir# exec < fdata > fout
root@t14:/home/bill/Documents/testdir# echo -n "The time now is:"
root@t14:/home/bill/Documents/testdir# date
root@t14:/home/bill/Documents/testdir# echo -n "The users presently logged on are:"
root@t14:/home/bill/Documents/testdir# who
root@t14:/home/bill/Documents/testdir#
root@t14:/home/bill/Documents/testdir# exit
bill@t14:~/Documents/testdir$ cat fout
The time now is:2023年 07月 09日 星期日 19:34:32 CST
The users presently logged on are:bill     seat0        2023-07-08 13:40 (login screen)
bill     tty2         2023-07-08 13:40 (tty2)
bill     pts/1        2023-07-09 16:40 (192.168.216.1)
bill     pts/2        2023-07-09 19:17 (192.168.216.1)
bill     pts/4        2023-07-08 19:45
```

<br/>
<font face = "黑体">

38.	*计算命令ls -l的输出中的字符数、单词数和行数，并把它显示在显示器上。给出命令和输出结果。*

</font>

使用wc (word count)命令计算文件或标准输入中的字符数、单词数和行数。将 ls -l 的输出通过管道传递给 wc 命令。
```bash{.line-numbers}
bill@t14:~/Documents/testdir$ ls -l | wc
     10      83     505
```

<br/>
<font face = "黑体">

39.	*在/bin目录下有多少个普通文件、目录文件和符号链接文件？如何得到这个答案？*

</font>


会话如下，使用 find 命令结合 -type 选项来统计 /bin 目录下的普通文件、目录文件和符号链接文件，通过管道传递给 wc -l 命令，以计算行数（即文件数量）。
```bash{.line-numbers}
bill@t14:/bin$ find . -type l | wc -l
283
bill@t14:/bin$ find . -type f | wc -l
1416
bill@t14:/bin$ find . -type d | wc -l
1
bill@t14:/bin$ find . -type d | ls -ld
drwxr-xr-x 2 root root 53248  7月  9 17:15 .
bill@t14:/bin$ find . -type l | ls -l | head -3
总计 282120
-rwxr-xr-x 1 root   root       55744  1月 10 21:19 [
-rwxr-xr-x 1 root   root       18744  3月  1 06:18 aa-enabled
```


## ​四、讨论，心得

#### 实验心得体会
本次实验主要是掌握shell的一些基本命令以及熟悉linux系统的基本的结构与规则。39个实验虽然数量上较多，但是每个实验都比较简单。通过回顾课件和教材以及上网查询资料，我很快地完成了这些实验。实验相对简单，所以记忆中没有遇到很困惑的地方。实验一为后续的几个实验打下基础，不过在写此心得时后续几个实验也基本完成了。个人感觉实验一和实验二的跨度偏大，比如实现简易版vim时需要的shell命令大部分是实验一中没有提到的。
通过本次实验，我对Linux的命令行操作有了初步的了解，并学会了如何获取帮助信息以及使用一些最常用的命令。以下是我在实验中印象深刻的一些知识点：

1. **命令格式和帮助信息：** 实验中，我学会了Linux命令的基本格式，并掌握了如何获取命令的帮助信息。通过使用`man`命令和`--help`选项，我能够查看命令的详细说明和使用方法，这对于学习和掌握新的命令非常有帮助。

2. **文件和目录操作：** 在实验中，我练习了使用`vi/vim`编辑器来编辑文本文件，并学会了创建Linux目录的层次结构。我了解了绝对路径和相对路径的概念，以及主目录和工作目录的区别。通过使用命令如`ls`、`cp`、`mv`、`mkdir`等，我能够浏览、复制、移动和删除文件，以及创建和管理目录。

3. **文件属性和访问权限：** 学习了Linux文件的属性和访问权限相关知识，我能够确定文件的大小，并理解了文件访问权限的不同类型。通过使用`chmod`命令，我学会了如何设置和改变文件的访问权限，以及如何在文件或目录创建时设置默认访问权限。

4. **文件内容显示和操作：** 通过使用命令如`cat`、`nl`、`head`、`tail`等，我学会了如何显示文本文件的内容，并掌握了一些常用的文件内容操作技巧。我了解了文件的不同类型和隐含文件的概念，可以根据需要合并文件内容。

5. **链接和进程管理：** 在实验中，我学习了硬链接和符号链接的概念，并了解了Linux进程的属性。我理解了前台进程、后台进程和守护进程的区别，以及命令的顺序执行和并发执行的概念。通过使用命令如`ps`、`kill`、`fg`、`bg`、`top`等，我能够管理和监控系统中的进程。

6. **I/O重定向和管道操作：** 实验中，我学习了如何使用I/O重定向操作和管道操作来处理命令的输入和输出。通过使用`>`、`<`、`|`等符号，我可以将命令的输出重定向到文件或从文件中读取输入，并将多个命令通过管道连接起来，实现更复杂的操作。

这些命令在日常的系统管理和开发工作中非常有用。我相信这些所学将为我在Linux环境下的工作和学习提供很大的帮助。
