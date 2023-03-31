# Qt获取Windows屏幕尺寸

.pro文件添加代码

```c++
win32 {
    msvc:LIBS += User32.lib
    msvc:LIBS += gdi32.lib
}
```

添加头文件

```c++
#include <Windows.h> //这个一定要添加，不然会报"No Target Architecture"错误
#include <WinUser.h>
#include <wingdi.h>
```

添加代码

```c++
int width = GetSystemMetrics(SM_CXSCREEN); //屏幕宽度
int height = GetSystemMetrics(SM_CYSCREEN); //屏幕高度
```

