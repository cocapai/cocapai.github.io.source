---
title: "从入门到放弃系列之c++web框架crow"
date: 2014-08-29T12:36:35+08:00
draft: true
tag: ["c++","Crow"]
categories: ["工作"]
cover: "https://www.cocapai.com/uploads/20181117220632243.png"
---


## 从入门到放弃系列之c++web框架crow

环境
 centos7
 cmake
 boost
 g++ 11
 crow
 clion

安装 cmake boost  使用yum 就可以
 sudo yum install -y  boost cmake g++,
 之后是安装 crow ，从github 克隆下来



```bash
cd /usr/local  or cd  /opt

git clone  https://github.com/ipkn/crow.git
cd  crow 
 mkdir build
cd build
cmake ..
make
ctest 
```

如果没有问题 ，crow 大致就可以安装成功了， 在根目录有一个example 的目录
 里面有二进制的 helloworld 文件可以 测试  最简单的 http 请求  ，端口 18080

#### 进入正题
 打开 clion ， 新建项目，选择 C++ Executable , language standard 最低 c++11
 然后进入项目窗口， 默认 clion 的c++项目使用 cmake 管理， 每一个项目会有一个
 CMakeLists.txt文件类似  java 的maven  pom.xml文件， 算是 项目的声明和依赖项 都在这里面声明

crow 是一个 c++的web microservice framework ，他依赖 boost ，所以你在项目中要引入两个第三方的库 boost 和crow ，不引入的话是无法识别的，怎么引入呢。
 boost 是一个比较重的组件，要引入很多内容， 而 crow 只需要 引入他的头文件的路径即可

比如我的项目叫  heseven

先说引入crow  简单，首先我的crow 安装路径是  /opt/crow/
 crow的头文件  crow.h  所在路径在 /opt/crow/include/ 目录中
 要引入crow 依赖，直接在  CMakeLists.txt 声明一行就可以了



```ruby
include_directories( /opt/crow/include/)
```

然后你在你的cpp 文件里 直接使用  #include "crow.h" 就可以，不过首次没有编译也会报错冒红线 ，需要一次编译才可以正常使用

然后是引入 boost ，boost  真的是老大难， 要声明的内容，少一个都不可以

下面我把下面的主要内容粘贴给你



```bash
set(BOOST_ROOT "/usr/local/include/boost")
set(BOOSTROOT "/usr/local/include/boost")
include_directories(/usr/local/include)
link_directories(/usr/local/lib)

FIND_PACKAGE(Threads REQUIRED)
SET(Boost_USE_MULTITHREADED ON)

find_package(Boost REQUIRED COMPONENTS system)
add_executable(heseven main.cpp)
target_link_libraries(heseven boost_thread boost_system ${CMAKE_THREAD_LIBS_INIT})
```

需要注意的是   里面的heseven 要用你的实际的项目名来替代
 然后 编译一下，成功的话，所有的包就都引入了，其实主要是其路径

还要注意点 boost 的一些环境变量，单独的g++ 编译的话，需要指明



```undefined
-lboost_system -lboost_filesystem
```

如果boost 引入不成功的话，会报
 boost中g++ 链接undefined reference to `boost::system::generic_category()问题

然后我们在 项目的main.cpp中编写  crow 最简单的 请求



```cpp
#include <iostream>
#include "crow.h"

int main()
{
    crow::SimpleApp app;

    CROW_ROUTE(app, "/")([](){
        return "Hello world are you ok ";
    });

    app.port(18080).multithreaded().run();
}
```

然后 启动项目，稍等一会，正常编译 ，开始监听18080 端口 ，然后



```cpp
 curl http://localhost:18080
```

成功的话  就可以收获返回值了

整体来说，一个半小时基本上是摸索入门了 ，
#### 推荐阅读
 C++ micro web framework -- crow
 https://www.jianshu.com/p/d2a8c740ac55

https://github.com/ipkn/crow

还有其他的 web framework
 https://github.com/matt-42/silicon
 https://github.com/danielwaterworth/Raphters
 https://github.com/topcpporg/cinatra
 https://github.com/sumeetchhetri/ffead-cpp
 https://github.com/treefrogframework/treefrog-framework
 https://github.com/sumeetchhetri/ffead-cpp

同时推荐 一些c++ 的 orm 框架
 https://github.com/david-pp/tiny-orm
 https://blog.csdn.net/Tulaimes/article/details/71403022
 https://blog.csdn.net/adrian169/article/details/8996325

https://blog.csdn.net/xiaoxiaoyeyaya/article/details/42541419

总体来说  需要探索的内容很多