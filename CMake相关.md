# 文件

- 有.cmake文件和CMakeLists.txt，.cmake文件与CMakeLists.txt一样，加载后可以在CMakeList.txt中使用它的一些函数和定义。

# CMake的重要指令

- **==cmake_minimum_required==** - 制定CMake的最小版本要求
  - 语法：cmake_minimum_required(VERSION versionNumber [FATAL_ERROR])

  - eg. cmake_minimum_required(VERSION 3.0)

- project - 定义工程名称，并可制定工程支持的语言
  - 语法：project(projectname [CXX]\[C]\[Java])
  - eg. project(HELLOWORLD)

- set - 显式的定义变量
  - 语法：set(VAR [VALUE]\[CACHE TYPE DOCSTRING [FORCE]])
  - set(SRC sayhello.cpp hello.cpp)

- include_directories - 向工程添加多个特定的**头文件搜索路径** ---->相当于制定g++编译器的-l参数
  - 语法：include_directories([AFTER | BEFORE]\[SYSTEM] dir1 dir2 ...)
  - include_directories(/usr/include/myincludefolder ./include)

- link_directories - 向工程添加多个特定的**库文件搜索路径** ---->相当于指定g++编译器的-L参数
  - 语法：link_directories(dir1 dir2)
  - link_directories(/usr/lib/mylibfolder ./lib)

- add_library - 生成库文件
  - 语法： add_library(libname [SHARED|STATIC|MODULE] [EXCLUDE_FROM_ALL] source1 source2 ... sourceN)
  - add_library(hello SHARED ${SRC}) **通过变量SRC生成libhello.so共享库，也就是将sayhello.cpp和hello.cpp合起来生成一个hell==动态库==文件

- add_executable - 生成可执行文件
  - 语法：add_executable(exename source1 source2 source3)
  - add_executable(main main.cpp) **编译main.cpp生成可执行文件main**

- target_link_libraries - 为target添加需要链接的共享库 --->相同于指定g++编译器-l参数
  - target_link_libraries(target library1 <debug|optimizied> library2> ...)
  - target_link_libraries(main hello) **将hello动态库文件链接到可执行文件main，==链接这个动态库后main才可以成功被执行==**

- aux_source_directory - 发现一个目录下所有的源代码文件并将列表存储在一个变量中，这个指令临时被用来自动构建源文件列表
  - aux_source_directory(dir VARIABLE)
  - aux_source_directory(. SRC) **定义SRC变量，其值为当前目录下所有的源代码文件**

- add_compile_options - 添加编译参数

  - 语法：add_compile_options()
  - add_compile_options(-Wall -std=c++11 -O2) // -Wall表示输出与所有的警告信息；-O2表示一个优化编译选项

- add_subdirectory - 向当前工程添加存放源文件的子目录，并可以指定中间二进制和目标二进制存放的位置

  - add_subdirectory(source_dir [binary_dir]\[EXCLUDE_FROM_ALL])
  - add_subdiretory(src)  **添加src子目录，src中需要有一个CMakeList.txt**

- `add_definitions` 是 CMake 中的一个命令，用于向编译器添加全局的预处理器定义。其主要作用是为编译过程添加预定义宏，这些宏可以在代码中使用 `#ifdef`、`#ifndef`、`#if` 等预处理指令进行条件编译

  ```cmake
  add_definitions(-D<macro> [options])
  ```

  一般用这种语法来动态定义模块中的使用的导出宏，比如在doc模块的cmake内add_definitions(-DBIM_DOC_EXPORT)，表示在该模块预定义了BIM_DOC_EXPORT宏，在定义导出选项时就可以动态选择了

  ~~~cmake
  #ifdef BIM_DOC_MODULE
  #define BIM_DOC_EXPORT   ZW_EXPORT
  #else
  #define BIM_DOC_EXPORT   ZW_IMPORT
  ~~~

- `add_dependencies` 是 CMake 中的一个命令，用于指定构建目标之间的依赖关系。该命令可以确保在构建一个目标之前，先构建它依赖的其他目标。这在构建复杂项目时特别有用，可以确保构建顺序的正确性。语法

```
cmake
复制代码
add_dependencies(<target> <dependency> [<dependency>...])
```

- `<target>`：需要等待其他目标完成构建后再构建的目标。
- `<dependency>`：`<target>` 依赖的一个或多个其他目标。

- `FILE(GLOB_RECURSE ...)` 是 CMake 中用于收集文件路径的命令，尤其适用于递归搜索特定目录及其子目录中的文件。FILE(GLOB_RECURSE ALL_HEADER ${PROJDIR}/*.h)这个表示从指定目录开始，递归查找所有的.h文件，并赋予变量。
- source_group(main FILES ${SOURCES}) source_group命令创建筛选器

# 最小CMake工程

\# 设置Cmake需要的最小版本

~~~cmake
cmake_minimum_required(VERSION 3.0)
~~~

\# 设置项目名称

project(HELLO)

\# 添加一个可执行的

add_executable(hello_cmake main.cpp)

# 多目录工程——直接编译

~~~CMAKE 
\# 设置CMake的最小版本
cmake_minimum_required(VERSION 3.0)

\# 项目名称
project(SWAP)

\# 头文件目录
include_directories(include)

\# 变量原目录
add_subdirectory(src DIR_SRCS)

\# 添加可执行
add_executable(swap_02 ${TEST_MATH})

\# 添加lib链接
target_link_libraries(\${FS\_BUILD\_BINARY\_PREFIX}sqrt, ${LIBRARIES}) 
~~~

# CMake常用变量

- CMAKE_C_FLAGS gcc编译选项

- CMAKE_CXX_FLAGS g++编译选项

  ~~~cmake
  # 在CMAKE_CXX_FLAGS编译选项后追加-std=C++11
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=C++11")
  ~~~

- ==CMAKE_BUILD_TYPE 编译类型(Debug，Release)==

  ~~~cmake
  # 设定编译类型为debug，调试时需要选择debug
  set(CMAKE_BUILD_TYPE Debug)
  # 设定编译类型为release，发布时需要选择release
  set(CMAKE_BUILD_TYPE Release)
  ~~~

- CMAKE_SOURCE_DIR为内建变量，表示工程根目录的CMakeLists.txt的路径

- CMAKE_CL_64 内建变量，在VS等64位编译器中为TRUE

- 常用CMake内置命令和内置宏如下：

~~~cmake
cmake_minimum_required(VERSION 3.13) // 指定CMake的最低版本要求，如果不加会有警告
project(cmake_test)	                 // project命令定义项目的名称，这个可以个性化处理
set(CMAKE_CXX_STANDARD 14)	         // set命令为cmake内置命令，用来给变量赋值，这里CMAKE_CXX_STANDARD变量为cmake内置变量用来指定所需最低的C++版本，这里要求C++版本最低为C++14
add_executable(cmake_testapp main.cpp) // add_executable命令会添加一个生成可执行文件的“目标（target）”，目标的名字为cmake_testapp，其将从main.cpp被构建（build）
~~~

# 系统信息

- WIN32，在所有的win32平台为TRUE，包括cygwin
- UNIX，在所有的类UNIX平台为True，包括OSX金额cygwin

# if

- 如果给定的字符串或变量值是一个有效数字并且不等号或等号满足。表达式返回真：
  - if (variable LESS number)
  - if (string LESS number)
  - if (variable GREATER number)
  - if (string GREATER number)
  - if (variable EQUAL number)
  - if (variable EQUAL number)

# 其他内建变量

- message: 为用户显示一条消息

  ~~~cm
  message([STATUS|WARNING|AUTHOR_WARNING|FATAL_ERROR|SEND_ERROR] "message to display" ...)
  ~~~

  ~~~cma
  (无) = 重要消息；
   STATUS = 非重要消息；
   WARNING = CMake 警告, 会继续执行；
   AUTHOR_WARNING = CMake 警告 (dev), 会继续执行；
   SEND_ERROR = CMake 错误, 继续执行，但是会跳过生成的步骤；
   FATAL_ERROR = CMake 错误, 终止所有处理过程；
  ~~~

- OPTION

  打开/关闭(动态库) OPTION(ZS_GLOBAL_DYNAMIC "message" ON)

- source_group在IDE项目生成中定义一个源文件组，用法有如下两种：

  - source_group(\<name\> [FILES \<src\>...] [REGULAR_EXPRESSION \

    <regex\>])

  - source_group(TREE \<root\> [PREFIX \<prefix\>] [FILES \<src\>...])

- ADD_CUSTOM_TARGET添加一个给定名称的目标去执行给定命令

  ~~~cMAKE
  add_custom_target(Name [ALL] [command1 [args1...]]
                    [COMMAND command2 [args2...] ...]
                    [DEPENDS depend depend depend ... ]
                    [BYPRODUCTS [files...]]
                    [WORKING_DIRECTORY dir]
                    [COMMENT comment]
                    [JOB_POOL job_pool]
                    [VERBATIM] [USES_TERMINAL]
                    [COMMAND_EXPAND_LISTS]
                    [SOURCES src1 [src2...]])
  ~~~

  该目标没有输出文件并且总被认为过时。

  可以使用add_custom_command()命令去生成一个具有依赖项的文件。或者基于默认的无任何依赖的custom目标，使用add_depencies()命令去添加依赖。

- `ADD_DEFINITIONS(-DBIM_GUI_MODULE -DZW_GUI_MODULE)` 

  -D后跟源文件编译标志，比如上面代码就是定义标志为`BIM_GUI_MODULE`和`ZW_GUI_MODULE`。

- get_propoerty从作用域的某个对象获取某一属性

  ~~~CMAKE
  get_property(<variable>
               <GLOBAL             |
                DIRECTORY [<dir>]  |
                TARGET    <target> |
                SOURCE    <source>
                          [DIRECTORY <dir> | TARGET_DIRECTORY <target>] |
                INSTALL   <file>   |
                TEST      <test>   |
                CACHE     <entry>  |
                VARIABLE           >
               PROPERTY <name>
               [SET | DEFINED | BRIEF_DOCS | FULL_DOCS])
  ~~~

  `<variable>`变量指定了结果存放的位置，`第二个<>`指定了作用域范围，可以是全局，可以是某些目录或者TARGET等，第三个`<name>`指定了property属性的名称

- list操作

  cmake中的list是一组以；分割的字符串，可以以set命令创建比如`set(var a b c d e)`创建`a;b;c;d;e`的list，同时`set(var "a b c d e")`是创建一个只有一个元素的list

  - 读取

    list(LEHGTH \<LIST\> \<output variable\>) 返回列表的长度

    `list(GET <list> <element index> [<element index> ... ] <output variable>)` 基于索引返回列表元素存于变量

    ...[ 其余的用法点这里 ](https://cmake.org/cmake/help/latest/command/list.html)

# 函数和宏

## 宏

macro，在宏定义体中，需要引用参数的话，只能写\${参数}。宏定义会将所有\${参数}的地方简单粗暴的替换为变量对应值。形参在宏定义体中已经过时不能使用

# 函数

函数需要Foo(${var})式的定义

# 生成器表达式

- **在CMake生成构建系统的时候根据不同配置动态生成特定的内容**。

开发者无法提前确定某些配置，生成器表达式形如`$<...>`，可以嵌套，可以用在很多构建目标的属性设置和特定的CMake命令中。生成表达式被展开是在生成构建系统时候，所以不能通过解析配置CMakeLists.txt阶段的`message`命令打印

## 常用生成器表达式

- 布尔生成器表达式

  - 逻辑运算符 `$<BOOL:string>`, 如果字符串为空、`0`；不区分大小写的`FALSE`、`OFF`、`N`、`NO`、`IGNORE`、`NOTFOUND`；或者区分大小写以`-NOTFOUND`结尾的字符串，则为0，否则为1

  - 字符串比较

    `$<STREQUAL:string1, string2>`:判断字符串是否相等

    `$<EQUAL:value1, value2>`:判断数值是否相等

- 条件表达式

  **核心：主要有两个格式：**

  1 `$<condition:true_string>$`:如果条件为真，则结果为true_string，否则为空

  2 `$<IF:condition, str1, str2>`如果条件为真，则结果为str1, 否则为str2

  用途：比如要根据编译类型指定不同的编译选项

  可以像这样

  ~~~Cmake
  set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -g -O0")
  set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -g -O0")
  set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -O2")
  set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O2")
  ~~~

  使用生成器表达式可以简化为

  ~~~Cma
  add_compile_options("$<$<CONFIG:Debug>:-g;-O0>")
  add_compile_options($<$<CONFIG:Release>:-O2>)
  ~~~

# 链接

```cmake
# 链接静态库
find_library(STATIC_LIB libStatic.lib PATHS "${CMAKE_SOURCE_DIR}/libs/static")
target_link_libraries(MyApp PRIVATE ${STATIC_LIB})

# 链接动态库
find_library(DYNAMIC_LIB libDynamic.dll PATHS "${CMAKE_SOURCE_DIR}/libs/dynamic")
find_library(DYNAMIC_LIB_IMPORT libDynamic.lib PATHS "${CMAKE_SOURCE_DIR}/libs/dynamic")
target_link_libraries(MyApp PRIVATE ${DYNAMIC_LIB_IMPORT})
# 链接动态库时，也是要链接.dll的对外入口库.lib文件
```


# 项目中的CMAKE搭建理解

1. 项目中CMAKELISTS文件有

   ~~~cmake
   SET(ZS_LIBRARY_USE_MODULES_PRIVATE
   BimBackend
   BimBase
   BimDatabase
   BimGui
   BimPmDtabase
   BimViewItem)
   ~~~

   该意思是将以上项目库名称设置为ZS_LIBRARY_USE_MODULE_PRIVATE变量

   **在cmakemodules的宏设置文件ZSoftMacroUtils.cmake里，会有如下设置**

   ~~~cmake
   TARGET_LINK_LIBRARY(${ZS_LIBRARY_NAME} PRIVATE ${ZS_LIBRARY_USE_MODULE_PRIVATE})
   // 该指令的作用是将目标文件与库文件链接
   // 上述指令中的<target>是指通过add_executable()和add_library()指令生成已经创建的目标文件。
   ~~~

   其中PRIVATE/PUBLIC/INTERFACE解释如下：（**需要注意的是，在CMAKE中好像这三个没有区别，都是对.h和.cpp都可见**）

   > 当创建动态库时，
   >
   > 如果源文件(例如CPP)中包含第三方头文件，但是头文件（例如hpp）中不包含该第三方文件头，采用PRIVATE。
   > 如果源文件和头文件中都包含该第三方文件头，采用PUBLIC。
   > 如果头文件中包含该第三方文件头，但是源文件(例如CPP)中不包含，采用 INTERFACE。
   >
   > ---------------------------------------------------------------------------------------
   >
   > ==0325理解==：当创建一个动态库时，如果希望该头文件只在库的dll里面可见，而其他引用该库的dll不可见，则可以用private；如果希望该头文件可在动态库的导出lib文件中可被其他模块看见，但是公开include文件不可见，则可以用INTERFACE；如果希望该头文件可以被公开通过include可见，则可以用public。

2. 与上一个类似

   ~~~cmake
   SET(ZS_LIBRARY_INCLUDE_DICTIONARIES_PRIVATE
   ${BIM_SOURCE_DIR}/BimBackend/include)
   ~~~

   **在cmakemodules的宏设置文件ZSoftMacroUtils.cmake里，会有如下设置**

   ~~~cmake
   TARGET_INCLUDE_DIRECTORIES(${ZS_LIBRARY_NAME} PRIVATE ${ZS_LIBRARY_INCLUDE_DICTIONARIES_PRIVATE})
   // 该命令可以指定目标（exe或者so文件）需要包含的头文件路径
   ~~~

   - 在cmake代码中，`include_directories`和`target_include_directories`命令都可以包含头文件，其中：
     - `include_directories`是全局包含的，项目中所有子目录都能够引用。
     - `target_include_directories`是针对某个二进制文件的，能够更好的控制访问的粒度。

3. 在项目CMAKE中，__ZS_PLATFORM_LINK_LIBRARYIES链接平台库，是指链接悟空那边的库，目前一个项目的CMAKE主要要有以下几个部分

   - ZS_LIBRARY_INCLUDE 在该项目生成库的过程中，需要用到的本项目的头文件
   - ZS_LIBRARY_SRC 在该项目生成库的过程中，需要用到的本项目的cpp文件，包括自己写的cpp文件外，还有平台的cpp文件\${_ZS_PLATFORM_SRC}
   - __ZS_PLATFORM_LINK_LIBRARIES 链接其他dll文件相对应的导出声明.lib文件
   - ZS_LIBRARY_INCLUDE_DIRECTORIES_PRIVATE private为仅供当前项目的cpp文件使用的头文件，当前项目的头文件看不到这些信息
   - ZS_LIBRARY_USE_MODULES_PRIVATE private为仅供当前项目的cpp文件使用的库文件，当前项目的头文件看不到这些信息

4. 保密层级projectInclude（悟空内部使用） > platformInclude （下游行业应用）> sdkInclude（下游应用二次开发）

5. 实际上对于use_modules_private和zs_platform_link_libraries到上层变量全部解析后，都会走到TARGET_LINK_LIBRARIES指令里面，只是对于use_modules中的模块，会在cmake中指定add_dependencies到zs_platform_link_libraries


# CMAKE和编译

- CMake和编译(或者make.)时软件开发中的两个不同步骤
- CMake是一个跨平台的构建工具，用于==生成针对不同操作系统和编译器==的**构建脚本**，**CMake的配置选项里有一个-G，表示使用的生成器，即generator，如果是64位vs，命令为cmake -G "Vistual Studio 15 2017 Win64"**。执行CMake命令会根据配置文件生成构建文件。
- 编译（make.）是将源代码转换为可执行文件或者库的过程，会基于CMake出来的构建脚本进行编译，因此如果在cmake时指定构建32位的编译文件，基于该构建脚本编译后的可执行文件/库则仅可被32位机器运行。**编译过程包括预处理，编译，汇编和链接等阶段。**

# CMAKE运行逻辑

- 首先，一个CMake要想运行，必须在同目录下有CMake脚本，说说是脚本，其实并没什么可怕，说白了就是一串CMake作者自己写的token语法分析文件--CMakeLists.txt。

- 这个文件比如你的项目有很多文件夹，必须在每个源代码文件夹下都有一个CMakeLists.txt.==它会根据CMake命令中的add_subdirectory自动的递归分析==。

- 在CMake中命令是不区分大小写的，而变量跟C一样是区分大小写的。

- cmake中主要处理了模块的依赖和链接关系，以及添加模块依赖的头文件路径（最原始的命令是include_directories）。这样在编译（翻译cpp文件）时才能将#include的文件找到并拉入翻译单元。**同时在include_directories中添加了头文件路径后，在 add_library(Hello hello.cxx/或者变量)指定cmake所在文件夹要形成的项目/库名称，以及对应的翻译单元（cpp文件）**

- cmake在构建时，一般使用out-of-source build，生成的MakeFile文件。对于一个code文件夹。

  ~~~cmake
  mkdir build && cd build
  cmake ../code -DCMAKE_BUILD_TYPE=Release
  cmake --build . -- -j$(nproc)
  (--build . 是让 CMake 调用实际的构建工具（make/ninja/msbuild）去编译当前目录（.）里的工程；
  后面的 -- 是分隔符，表示后面的参数不要给 cmake 本身，而是直接转发给底层的构建工具；
  -j(nproc)是底层构建工具的并行参数（让它用多核并行编译），其中-j是底层构建工具的并行参数（让它用多核并行编译），其中(nproc) 在类 Unix shell 下会被替换为 CPU 核心数。)
  
  注意：第三行cmake --build .实际上是一个更安全，跨平台的写法，基于cmake时的生成器去自动调用工具构建（在 Unix Makefiles 下它内部会运行 make；在使用 Ninja 时会运行 ninja；在 Windows 会运行 MSBuild/Visual Studio 等）。在Makefile下，等同于make -j$(nproc)
  ~~~

- CMAKE（而非构建）运行后，顶层一般会看到CMAKEFiles/(用于驱动构建的中间文件)，CMakeCache、Makefile等文件，另外如果还有下层CMake(顶层CMAKELIST中有Add_SubDirectory等命令)，也会递归的生成对应的文件夹，以及下层CMAKE中间文件。
- 基于Make构建后的生成内容和位置：构建后的内容包括可执行文件、库文件、中间文件(.o等)、Cmake中间文件等。
- 这些构建生成的文件会被默认放到每一个CMAKE对应的二进制目录下（CMAKE_CURRENT_BINARY_DIR），一般默认为对应MakeFile的当前目录，具体相对文件数完全等同，类似只是换了一个根目录，相对目录不变。

