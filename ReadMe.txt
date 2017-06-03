cmake命令 安装、用法简介
标签： cmake开发人员
2015-11-13 11:46 12935人阅读 评论(0) 收藏 举报
 分类： linux（34）  
目录(?)[+]
前言

cmake是kitware公司以及一些开源开发者在开发几个工具套件(VTK)的过程中所产生的衍生品。后来经过发展，最终形成体系，在2001年成为一个独立的开放源代码项目。其官方网站是www.cmake.org，可以通过访问官方网站来获得更多关于cmake的信息，而且目前官方的英文文档比以前有了很大的改进，可以作为实践中的参考手册。

cmake的流行离不开KDE4的选择。KDE开发者在使用autotools近10年之后，终于决定为KDE4项目选择一个新的工程构建工具。之所以如此，用KDE开发者们自己话来说，就是：只有少数几个“编译专家”能够掌握KDE现在的构建体系。在经历了unsermake，scons以及cmake的选型和尝试之后，KDE4最终决定使用cmake作为自己的构建系统。在迁移过程中，进展一场的顺利，并获得了cmake开发者的支持。所以，目前的KDE4开发版本已经完全使用cmake来进行构建。

随着cmake 在KDE4项目中的成功，越来越多的项目正在使用cmake作为其构建工具，这也使得cmake正在成为一个主流的构建体系。

为何要使用项目构建工具

为何要使用cmake和autotools之类的项目构建工具？ 我想，这恐怕是刚刚接触软件项目的人最应该问的问题之一了。

“Hello, world！“这个最经典的程序相信我们每个人都写过。无论在什么平台下，编译和运行这个程序都仅需要非常简单的操作。但事实上，hello,world最多只能算是一个实例程序，根本算不上一个真正的软件项目。

任何一个软件项目，除了写代码之外，还有一个更为重要的任务，就是如何组织和管理这些代码，使项目代码层次结构清晰易读，这对以后的维护工作大有裨益。使想一下，如果把一个像KDE4那么大的项目像hello world那样，把全部代码都放到一个main.cpp文件中，那将会是多么恐怖的一件事情。别说KDE4，就是我们随便一个几千行代码的小项目，也不会有人干这种蠢事。

决定代码的组织方式及其编译方式，也是程序设计的一部分。因此，我们需要cmake和autotools这样的工具来帮助我们构建并维护项目代码。 
看到这里，也许你会想到makefile，makefile不就是管理代码自动化编译的工具吗？为什么还要用别的构建工具？

其实，cmake和autotools正是makefile的上层工具，它们的目的正是为了产生可移植的makefile，并简化自己动手写makefile时的巨大工作量。如果你自己动手写过makefile，你会发现，makefile通常依赖于你当前的编译平台，而且编写makefile的工作量比较大，解决依赖关系时也容易出错。因此，对于大多数项目，应当考虑使用更自动化一些的 cmake或者autotools来生成makefile，而不是上来就动手编写。

总之，项目构建工具能够帮我们在不同平台上更好地组织和管理我们的代码及其编译过程，这是我们使用它的主要原因。

cmake的主要特点

cmake和autotools是不同的项目管理工具，有各自的特点和用户群。存在即为合理，因此我们不会对两者进行优劣比较，这里只给出cmake的一些主要特点： 
1.开放源代码,使用类 BSD 许可发布。 
2.跨平台,并可生成 native 编译配置文件,在 Linux/Unix 平台,生成 makefile,在 苹果平台,可以生成 xcode,在 Windows 平台,可以生成 MSVC 的工程文件。 
3.能够管理大型项目,KDE4 就是最好的证明。 
4.简化编译构建过程和编译过程。Cmake 的工具链非常简单:cmake+make。 
5.高效率，按照 KDE 官方说法,CMake 构建 KDE4 的 kdelibs 要比使用 autotools 来 构建 KDE3.5.6 的 kdelibs 快 40%,主要是因为 Cmake 在工具链中没有 libtool。 
6.可扩展,可以为 cmake 编写特定功能的模块,扩充 cmake 功能。

安装cmake

安装cmake 对任何用户而言都不该再成为一个问题。几乎所有主流的Linux发行版的源中都包含有cmake的安装包，直接从源中添加即可。当然，也可以在官方网站下载源代码自行编译安装。

对于Windows和Mac用户，cmake的官方网站上有相应的安装包，下载安装即可，无须赘述。

注：为了能够测试本文中的实例程序，如果读者的Linux系统中所带的cmake版本低于2.6，请从官网下载2.6版本或以上的源代码进行编译并安装。

在linux下安装cmake

首先下载源码包 
http://www.cmake.org/cmake/resources/software.html

这里下载的是cmake-2.6.4.tar.gz

随便找个目录解压缩

      tar -xzvf cmake-2.6.4.tar.gz
        cd cmake-2.6.4
1
2
1
2
依次执行：

     ./bootstrap
     make
     make install
1
2
3
4
1
2
3
4
cmake 会默认安装在 /usr/local/bin 下面

Hello, world!

了解cmake的基本原理并在系统中安好cmake后，我们就可以用cmake来演示那个最经典的”Hello, world!”了。

第一步，我们给这个项目起个名字——就叫HELLO吧。因此，第一部为项目代码建立目录hello，与此项目有关的所有代码和文档都位于此目录下。

第二步，在hello目录下建立一个main.c文件，其代码如下：

     #include <stdio.h>
     int main(void)
     {
             printf(”Hello,World\n”);
             return 0;
     }
1
2
3
4
5
6
1
2
3
4
5
6
第三步，在hello目录下建立一个新的文件CMakeLists.txt，它就是 cmake所处理的“代码“。其实，使用cmake管理项目本身也是在编程，所以称之为“代码（或脚本）”并不为过。在CMakeLists.txt文件中输入下面的代码(#后面的内容为代码行注释)：

      #cmake最低版本需求，不加入此行会受到警告信息
      CMAKE_MINIMUM_REQUIRED(VERSION 2.6)
      PROJECT(HELLO) #项目名称
      #把当前目录(.)下所有源代码文件和头文件加入变量SRC_LIST
      AUX_SOURCE_DIRECTORY(. SRC_LIST)
      #生成应用程序 hello (在windows下会自动生成hello.exe)
      ADD_EXECUTABLE(hello ${SRC_LIST})
1
2
3
4
5
6
7
1
2
3
4
5
6
7
至此，整个hello项目就已经构建完毕，可以进行编译了。

第四步，编译项目。

为了使用外部编译方式编译项目，需要先在目录hello下新建一个目录build(也可以是其他任何目录名)。现在，项目整体的目录结构为：

      hello/
        |– CMakeLists.txt
        |– build /
        `– main.c
1
2
3
4
1
2
3
4
在windows下，cmake提供了图形界面，设定hello为source目录，build为二进制目录，然后点击configure即可开始构建，之后进入build目录运行make命令编译。

在linux命令行下，首先进入目录build，然后运行命令(注：后面的“..”不可缺少)：

该命令使cmake检测编译环境，并生成相应的makefile。接着，运行命令make进行编译。编译后，生成的所有中间文件和可执行文件会在build目录下。 下面是我在ubuntu上的运行过程：

        $ ls
        hello
        $ cd hello/build/
        $ ls
        $ cmake ..
        – The C compiler identification is GNU
        – The CXX compiler identification is GNU
        – Check for working C compiler: /usr/bin/gcc
        – Check for working C compiler: /usr/bin/gcc — works
        – Detecting C compiler ABI info
         – Detecting C compiler ABI info - done
        – Check for working CXX compiler: /usr/bin/c++
        – Check for working CXX compiler: /usr/bin/c++ — works
        – Detecting CXX compiler ABI info
        – Detecting CXX compiler ABI info - done
        – Configuring done
        – Generating done
        – Build files have been written to: /home/kermit/Project/cmake/hello/build
        $ make
        Scanning dependencies of target hello
        [100%] Building C object CMakeFiles/hello.dir/main.c.o
        Linking C executable hello
        [100%] Built target hello
        $ ls
        CMakeCache.txt CMakeFiles cmake_install.cmake hello Makefile
        $ ./hello
        Hello,World
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
Cmake生成已有项目

1、项目目录规划 
在project_dir下建立build, cmake, src三个目录。

2、编写CMakeLists.txt文件。

3、调用CMake命令为eclipse生成项目文件 
点击(此处)折叠或打开

mkdir -p build/release

mkdir -p build/debug

cmake -E chdir build/release cmake -G "Eclipse CDT4 - Unix Makefiles" -DCMAKE_BUILD_TYPE=release ../../

cmake -E chdir build/debug cmake -G "Eclipse CDT4 - Unix Makefiles" -DCMAKE_BUILD_TYPE=debug ../../
1
2
3
4
5
6
7
1
2
3
4
5
6
7
4、打开eclipse 
a. 选择菜单： 
File->Import->General->Existing Projects into Workspace 
b. 点击 “Next”，在 Select root directory 选择目录为project_dir/build 
Eclipse会自动搜索到debug, release两个目录下的项目。 
c. 点击“Finish”按钮。eclipse将自动加入项目。

剩下的就是使用Eclipse CDT 调试程序了。
续

上面，我们提到了一个名词，叫外部编译方式。其实，cmake还可以直接在当前目录进行编译，无须建立build目录。但是，这种做法会将所有生成的中间文件和源代码混在一起，而且cmake生成的makefile无法跟踪所有的中间文件，即无法使用”make distclean”命令将所有的中间文件删除。因此，我们推荐建立build目录进行编译，所有的中间文件都会生成在build目录下，需要删除时直接清空该目录即可。这就是所谓的外部编译方式。

转载网址，多谢原文博主精彩总结，转载至此，仅为学习方便，无抄袭动机，再次感谢！

