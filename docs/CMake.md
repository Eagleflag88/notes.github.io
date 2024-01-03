---
share: true
---

# MACRO

## ${PROJECT_SOURCE_DIR}
- CMakeLists.txt所在目录

## ${PROJECT_NAME}

## ${PROJECT_BINARY_DIR}
- build目录
- 一般为PROJECT_SOURCE_DIR/build

## ${CMAKE_CURRENT_SOURCE_DIR}
- 当前处理的 CMakeLists.txt 所在的路径

## ${CMAKE_SOURCE_DIR}
- 整个工程的顶层 CMakeLists.txt 所在的路径

## Ref
- https://www.cnblogs.com/xianghang123/p/3556425.html

# Directive

## configure_file()
- 配置一个头文件

## add_subdirectory (source_dir [binary_dir])
- 添加外部项目文件夹
- source_dir
	- 外部项目的地址：这个里面也有CMakeList文件，CMakeList里的内容就会被执行
- binary_dir
		- 当source_dir是原项目的CMakelist的下级目录的话：binary_dir省略
		- 当source_dir不是源项目的CMakelist的下级目录的话 → 需要写成target_binary_dir
- 生成的target可以通过target_link_libraries()来链接
- Ref： https://zhuanlan.zhihu.com/p/85980099

## add_dependencies(target, dependencies)
- 告诉编译器，在链接生成target的时候，需要先生成dependencies
- dependencies也是从源码编译

## include_directories(${PROJECT_SOURCE_DIR}/include)
- 提供头文件的搜索路径

## add_executable(test test.cpp)
- 利用test.cpp生成可执行文件test

## aux_source_directory(dir variable)
- 寻找dir下的所有源文件，并把名字存入variable中
- 之后可以调用add_executable(test variable)来生成可执行文件

## add_library(${PROJECT_NAME} SHARED src/hello.cpp)

- 利用src/hello.ccp生成一个动态库的target：target名字为${PROJECT_NAME}
- 也可以是静态库：去除SHARED关键字
- 指的是本项目产生的库

## LINK_DIRECTORIES(directory1 directory2 ...)

- 为共享库添加搜索路径

## target_link_libraries(test target)

- test为库或二进制可执行文件添加库链接
- target是指通过add_executable()和add_library()指令生成已经创建的目标文件

## find_package(package)
- 参数
	- REQUIRED：如果没有找到，cmake会报错退出
	- QUITE：屏蔽cmake找不到package的信息
- 搜索模式
	- MODULE模式
		- 搜索CMAKE_MODULE_PATH下的FindXXX.cmake文件
		- 默认模式：搜索路径：/usr/share/cmake-3.19/Modules
	- CONFIG模式
		- 搜索XXX_DIR下的XXXConfig.cmake文件
		- 通过set(XXX_DIR CACHE PATH)设定搜索路径
	- 搜索完成后cmake会执行.cmake文件，赋值其中一些变量
- Ref： https://www.cnblogs.com/linuxAndMcu/p/10675971.html

## .cmake文件
- find_library(lib_dir lib.so/.a path)
	- 在path中寻找lib.so/.a，保存在lib_dir中
	- 然后target可以被链接
- find_path(include_dir .h path)
	- 在path中寻找.h，保存在include_dir中

# Ref
- https://cliutils.gitlab.io/modern-cmake/