# OpenCV学习笔记①——Qt调用OpenCV

## Qt调用OpenCV库

### 1.复制.dll文件到程序目录下的Bin文件夹

### 2..Pro文件添加代码

```c++
DESTDIR = $$PWD/Bin
INCLUDEPATH += D:\OpenCV4.5.4\opencv\build\include \
               D:\OpenCV4.5.4\opencv\install\install\include
LIBS += D:\OpenCV4.5.4\opencv\install\lib\libopencv_*.a
```

### 3.包含头文件

```c++
#include <opencv2/opencv.hpp>
```

