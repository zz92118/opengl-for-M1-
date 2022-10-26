# opengl-for-M1-

## 因为第一次opengl进行配置的时候踩了很多坑，所以这边记录一下踩坑的历程 and 安装过程


先通过homebrew安装 glfw和glew 很好 这一步确实没出什么问题

```shell
glfw ：brew install glfw
glew ：brew install glew
      brew install freeglut
```

然后去这个网站下找到对应的glad版本，点击generate按钮
https://glad.dav1d.de/

cmkae.txt中的代码，这个是本机上执行的版本
```
cmake_minimum_required(VERSION 3.23)
project(opengl_study)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_OSX_ARCHITECTURES  "arm64" CACHE STRING "Build architectures for Mac OS X" FORCE)

set(LOCAL_H /opt/homebrew/include) #得关注一下这一行代码 
include_directories(${LOCAL_H})

set(GLEW_H /opt/homebrew/Cellar/glew/2.2.0_1/include/GL)
set(GLFW_H /opt/homebrew/Cellar/glfw/3.3.5/include/GLFW)
link_directories(${GLEW_H} ${GLFW_H})

set(GLEW_LINK /opt/homebrew/Cellar/glew/2.2.0_1/lib/libGLEW.2.2.dylib)
set(GLFW_LINK /opt/homebrew/Cellar/glfw/3.3.5/lib/libglfw.3.dylib)
link_libraries(${OPENGL} ${GLEW_LINK} ${GLFW_LINK})

#add_executable(OpenGL src/glad.c src/main.cpp)
add_executable(OpenGL main.cpp)

if (APPLE)
    target_link_libraries(OpenGL "-framework OpenGL")
    target_link_libraries(OpenGL "-framework GLUT")
endif ()
```
好像是glad需要一起进行编译，得把glad文件夹底下的内容复制到

```
cp -r KHR/ /opt/homebrew/include/
cp -r glad/ /opt/homebrew/include/
```

执行
```
ls /opt/homebrew/include/
```
之后发现目录下有，好像并不是我预想的那样复制的？？？
```
GL		glad.h		src
GLFW		khrplatform.h	yaml.h
GL GLFW里面是需要的.h文件
src是glad文件夹里的glad.c
```

最后就是因为这行代码取消了报错，这里是最终的include路径吧
```
set(LOCAL_H /opt/homebrew/include) #得关注一下这一行代码 
```


目前glad上的回调函数ld symbol问题还是没有解决 回去拿win主机试一下吧


## 更新一下 ld symbol not found 的问题终于解决了
解决的方法是把glad.c添加到src文件下，并在cmakelist.txt下对 main.cpp 和 glad.c 一起编译 也就是加这段话

```
 add_executable(OpenGL src/glad.c src/main.cpp)
```
因为我include的路径是

```
set(LOCAL_H /opt/homebrew/include)

目录长这样，所以有些代码中include的头文件得改掉路径

GL		glad.h		src
GLFW		khrplatform.h	yaml.h
GL GLFW里面是需要的.h文件
src是glad文件夹里的glad.c

```

比如glad 里面我就把路径KHR\khrplatform.h 直接改成了 khrplatform.h
```
#include <khrplatform.h>
```
到这为止,glew,glfw,gad的使用都没有问题了

但glue好像一直没用到，这好像是个比较老的库了，所以感觉不太matter？
 glut failed to display.

## Reference
* https://zhuanlan.zhihu.com/p/498470512
* include glad的写法需要的时候可以参考 https://blog.csdn.net/suchvaliant/article/details/122747967
* 最一开始的版本 没有cp -r的动作，最后编译没有成功 https://blog.csdn.net/JasmineCAicai/article/details/119221390
* Mac下的配置 https://blog.csdn.net/suchvaliant/article/details/122747967 
* 环境测试的代码 https://learnopengl-cn.github.io/01%20Getting%20started/04%20Hello%20Triangle/ 
