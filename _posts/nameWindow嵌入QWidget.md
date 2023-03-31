```c++
/**********************************************************************
Function:    // namedWindowToWidget
Description: // 界面嵌入Opencv之namedWindow窗口
Input:       // std::string namedWindowTitle, QWidget * widget
Output:      // 无
Return:      // void
Author:      // 马文水
Time:        // 2022-03-16
**********************************************************************/
void InfraredSimulation::namedWindowToWidget(std::string namedWindowTitle, QWidget *widget)
{
    // 取 opencv 的 namedWindow窗体 句柄
    HWND hwnd = (HWND)cvGetWindowHandle(namedWindowTitle.c_str()); //转char*
    //取title窗体的父窗体句柄
    HWND hparent = ::GetParent(hwnd);
    //改变某个子窗体的父窗口(子窗口句柄,新的父窗口句柄)
    ::SetParent(hwnd, (HWND)widget->winId());  //winId()取win32api要用的窗口句柄
    //显示窗体 SW_SHOW  隐藏窗体 SW_HIDE
    ::ShowWindow(hparent, SW_HIDE);
}
```

