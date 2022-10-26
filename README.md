# opengl-for-M1-

## 因为第一次opengl进行配置的时候踩了很多坑，所以这边记录一下踩坑的历程 and 安装过程


先通过homebrew安装 glfw和glew 很好 这一步确实没出什么问题

```shell
glfw ：brew install glfw
glew ：brew install glew
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



## Reference
* https://zhuanlan.zhihu.com/p/498470512
* include glad的写法需要的时候可以参考 https://blog.csdn.net/suchvaliant/article/details/122747967
* 最一开始的版本 没有cp -r的动作，最后编译没有成功 https://blog.csdn.net/JasmineCAicai/article/details/119221390
