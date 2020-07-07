# 简介
本文主要介绍如何用docker编译openjdk8.


# 步骤
## 1.前置环境
         必须安装dokcer环境,然后才能执行下面的操作.

		镜像地址: https://github.com/BoYiZhang/compile-openjdk8-in-docker.git
		
         
## 2.获取资源文件
`git clone https://github.com/BoYiZhang/compile-openjdk8-in-docker.git`

## 3.进入目录: ubuntu1404_openjdk8
打开项目目录compile-openjdk8-in-docker进入目录ubuntu1404_openjdk8

`cd ubuntu1404_openjdk8`

## 4.编译
 
`docker build -t ubuntu1404_openjdk8:v1 . `

 **[注意命令后面的那个点[ . ]  ]**

```bash

## Finished images (build time 00:01:08)
----- Build times -------
Start 2020-07-07 03:48:44
End   2020-07-07 04:19:45
00:01:24 corba
00:01:01 demos
00:17:27 hotspot
00:01:08 images
00:00:36 jaxp
00:01:02 jaxws
00:06:57 jdk
00:00:51 langtools
00:00:35 nashorn
00:31:01 TOTAL
-------------------------
Finished building OpenJDK for target 'images'
Removing intermediate container 69bcdd4b0831
 ---> ba8a923bd614
Step 8/8 : RUN  ls -al /opt/openjdk
 ---> Running in ce954a436586
total 125072
drwxr-xr-x  1 root root      4096 Jul  7 03:48 .
drwxr-xr-x  1 root root      4096 Jul  7 03:46 ..
-rw-r--r--  1 root root 128058047 Jan 14 20:07 openjdk-8u41-src-b04-14_jan_2020.zip
drwxr-xr-x 14 root root      4096 Jul  7 03:48 openjdk8
Removing intermediate container ce954a436586
 ---> 23c0dfed1d0f
Successfully built 23c0dfed1d0f
Successfully tagged ubuntu1404_openjdk8:v1
MacBook-Pro:ubuntu1404_openjdk8 sysadmin$

```

## 5. 进入目录查看编译后的文件
`docker run -ti --entrypoint /bin/sh ubuntu1404_openjdk8:v1`


```bash
MacBook-Pro:ubuntu1404_openjdk8 sysadmin$
MacBook-Pro:ubuntu1404_openjdk8 sysadmin$ docker run -ti --entrypoint /bin/sh ubuntu1404_openjdk8:v1
#
# cd /opt/openjdk/openjdk8
#
# ls -al
total 440
drwxr-xr-x 14 root root   4096 Jul  6 18:06 .
drwxr-xr-x  1 root root   4096 Jul  6 18:06 ..
-rw-r--r--  1 root root     70 Jan 14 19:58 .hgignore
-rw-r--r--  1 root root  19704 Jan 14 19:58 .hgtags
drwxr-xr-x  2 root root   4096 Jan 14 19:58 .jcheck
-rw-r--r--  1 root root   1503 Jan 14 19:58 ASSEMBLY_EXCEPTION
-rw-r--r--  1 root root  19263 Jan 14 19:58 LICENSE
-rw-r--r--  1 root root   6232 Jan 14 19:58 Makefile
-rw-r--r--  1 root root   1549 Jan 14 19:58 README
-rw-r--r--  1 root root 129333 Jan 14 19:58 README-builds.html
-rw-r--r--  1 root root 178392 Jan 14 19:58 THIRD_PARTY_README
-rwxr-xr-x  1 root root   8612 Jul  6 18:06 a.out
drwxr-xr-x  3 root root   4096 Jul  6 18:06 build
drwxr-xr-x  6 root root   4096 Jan 14 19:58 common
-rwxr-xr-x  1 root root   1235 Jan 14 19:58 configure
drwxr-xr-x  5 root root   4096 Jan 14 19:58 corba
-rw-r--r--  1 root root   3095 Jan 14 19:58 get_source.sh
drwxr-xr-x  7 root root   4096 Jan 14 19:59 hotspot
drwxr-xr-x  6 root root   4096 Jan 14 19:59 jaxp
drwxr-xr-x  6 root root   4096 Jan 14 19:59 jaxws
drwxr-xr-x  6 root root   4096 Jan 14 20:02 jdk
drwxr-xr-x  6 root root   4096 Jan 14 20:02 langtools
drwxr-xr-x  6 root root   4096 Jan 14 19:58 make
drwxr-xr-x 12 root root   4096 Jan 14 20:03 nashorn
drwxr-xr-x  2 root root   4096 Jan 14 19:58 test
#
```
**代码结构如下:**
```bash
├─agent                            Serviceability Agent的客户端实现
├─make                             用来build出HotSpot的各种配置文件
├─src                              HotSpot VM的源代码
│  ├─cpu                            CPU相关代码（汇编器、模板解释器、ad文件、部分runtime函数在这里实现）
│  ├─os                             操作系相关代码
│  ├─os_cpu                         操作系统+CPU的组合相关的代码
│  └─share                          平台无关的共通代码
│      ├─tools                        工具
│      │  ├─hsdis                      反汇编插件
│      │  ├─IdealGraphVisualizer       将server编译器的中间代码可视化的工具
│      │  ├─launcher                   启动程序“java”
│      │  ├─LogCompilation             将-XX:+LogCompilation输出的日志（hotspot.log）整理成更容易阅读的格式的工具
│      │  └─ProjectCreator             生成Visual Studio的project文件的工具
│      └─vm                           HotSpot VM的核心代码
│          ├─adlc                       平台描述文件（上面的cpu或os_cpu里的*.ad文件）的编译器
│          ├─asm                        汇编器接口
│          ├─c1                         client编译器（又称“C1”）
│          ├─ci                         动态编译器的公共服务/从动态编译器到VM的接口
│          ├─classfile                  类文件的处理（包括类加载和系统符号表等）
│          ├─code                       动态生成的代码的管理
│          ├─compiler                   从VM调用动态编译器的接口
│          ├─gc_implementation          GC的实现
│          │  ├─concurrentMarkSweep      Concurrent Mark Sweep GC的实现
│          │  ├─g1                       Garbage-First GC的实现（不使用老的分代式GC框架）
│          │  ├─parallelScavenge         ParallelScavenge GC的实现（server VM默认，不使用老的分代式GC框架）
│          │  ├─parNew                   ParNew GC的实现
│          │  └─shared                   GC的共通实现
│          ├─gc_interface               GC的接口
│          ├─interpreter                解释器，包括“模板解释器”（官方版在用）和“C++解释器”（官方版不在用）
│          ├─libadt                     一些抽象数据结构
│          ├─memory                     内存管理相关（老的分代式GC框架也在这里）
│          ├─oops                       HotSpot VM的对象系统的实现
│          ├─opto                       server编译器（又称“C2”或“Opto”）
│          ├─prims                      HotSpot VM的对外接口，包括部分标准库的native部分和JVMTI实现
│          ├─runtime                    运行时支持库（包括线程管理、编译器调度、锁、反射等）
│          ├─services                   主要是用来支持JMX之类的管理功能的接口
│          ├─shark                      基于LLVM的JIT编译器（官方版里没有使用）
│          └─utilities                  一些基本的工具类
└─test                             单元测试

```

## 6. 复制文件到指定目录
使用 `docker ps -a` 获取到镜像的id, 然后使用下面的名称将编译后的文件复制出来. 
[当然编译的时候也可以把编译目录opt外挂到磁盘上,这样编译后的目录就直接在本地磁盘上上了]


`docker cp 884d465e7e2b:/opt/openjdk/openjdk8 /workspace`

```bash
MacBook-Pro:ubuntu1404_openjdk8 sysadmin$ docker ps -a
CONTAINER ID        IMAGE                          COMMAND                  CREATED             STATUS                      PORTS               NAMES
884d465e7e2b        ubuntu1404_openjdk8:v1   "/bin/sh"                9 minutes ago       Exited (0) 4 minutes ago                        jovial_blackwell
```

# Dockerfile 

```bash
FROM ubuntu:14.04
MAINTAINER BoYiZhang  [ https://github.com/BoYiZhang  ]
# backup data source
RUN mv /etc/apt/sources.list /etc/apt/sources.list.bak
# Replace data source
COPY ./sources.list /etc/apt/
# Installation dependency
RUN apt-get update \
    && apt-get install -y  curl ssh zip unzip vim ant git mercurial build-essential ccache cpio g++ gcc curl libx11-dev libxext-dev libxrender-dev libxtst-dev libxt-dev libcups2-dev libfreetype6-dev libasound2-dev libelf-dev openjdk-7-jdk gdb

# mkdir work dir
RUN mkdir -p /opt/openjdk 
# other ways to get the source of openjdk8
# RUN  hg clone  http://hg.openjdk.java.net/jdk8u/jdk8u/

# download soucecode && compile    
RUN cd /opt/openjdk && wget https://download.java.net/openjdk/jdk8u41/ri/openjdk-8u41-src-b04-14_jan_2020.zip && unzip openjdk-8*.zip && mv openjdk openjdk8 && cd openjdk8 && sed -i 's/3%/3% 4%/g' hotspot/make/linux/Makefile && chmod a+x configure && ./configure --with-debug-level=slowdebug --with-target-bits=64 --with-freetype-include=/usr/include/freetype2 --with-freetype-lib=/usr/lib/x86_64-linux-gnu && make clean && make images

# view compiled files 
RUN  ls -al /opt/openjdk
```