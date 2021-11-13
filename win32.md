## windows窗口编程  

### 两个重要的知识点

+ windows
    - class
    - instance
+ message

# 1.Windows    
### 1.入口函数代码  

```cpp
#include <Windows.h>

int CALLBACK WinMain(
    HINSTANCE hInstance,
    HINSTANCE hPrevInstance,
    LPSTR lpCmdLine,
    INT nCmdShow) 
```

win32的窗口是 __回调函数__ ，入口是 **WinMain**，程序执行时，系统将该函数当作参数传入调用函数中，并获取参数  

类型|参数|说明
---|---|---
HINSTANCE       |hInstance       |程序实例的句柄
HINSTANCE       |hPrevInstance   |程序先前实例的句柄
LPSTR  (char *) |lpCmdLine       |程序的命令行参数指针
INT             |nCmdShow        |窗口的显示方式

第三个参数 **LPSTR** 类似于<b style="color:pink">unicode编码环境下的char *</b>，windows为了避免不同环境下编码造成的问题重新设计了一套类型。

### 2.窗口创建代码

```cpp  

const auto pClassName = "MyClassName";//窗口类Class的唯一标识符

///code is here
WNDCLASSEX wc = {0};
wc.cbSize = sizeof(wc); //分配的空间
wc.style = CS_OWNDC;    //窗口的style
wc.lpfnWndProc = DefWindowProc; //消息处理函数
wc.cbClsExtra = 0; //额外需要分配给class的内存
wc.cbWndExtra = 0; //额外需要分配给Window的内存
wc.hInstance = hInstance; // 当前窗口的实例句柄
wc.hIcon = nullptr; //图标
wc.hCursor = nullptr; //光标
wc.hbrBackground = nullptr;//背景
wc.lpszMenuName = nullptr; //菜单栏
wc.lpszClassName = pClassName;
wc.hIconSm = nullptr;

RegisterClass( &wc );//注册窗口类

```

class是用来描述一个窗体的具体属性的，需要注册该class，然后才使用createWindow()函数具体的去规定一个窗口。

## 3.然后就是创建窗口了    

```cpp    
HWND hWnd = createWindowEx(
    0,          //optional window styles 额外的window style
    pClassName, //Class Name 窗口的唯一标识符
    L"Title",   //窗口的标题
    WS_OVERLAPPEDWINDOW, //Window Style
    //position and dimension
    x,y,width,height,
    nullptr,    //Parent window
    nullptr,    //Menu
    hInstance,  //当前窗口实例的句柄
    nullptr);    //额外的数据

showWindow( hWnd, SW_SHOW );
```

# 2.Message    

+ 从消息队列中获取消息
    - GetMessage()
    - PeekMessage()

```cpp
/**
  * GetMessage的函数定义
  * Return Value：true if not WM_EXIT
  */
BOOL GetMessage( //从队列中获取一条消息并删除，如果没有消息则阻塞。
  LPMSG lpMsg,   //MSG对象
  HWND  hWnd,    //如果指定了窗口，则只获取该窗口的消息，如果是nullptr，则获取所有窗口的消息
  UINT  wMsgFilterMin, //用于过滤消息
  UINT  wMsgFilterMax // min = max时，不过滤
);
```
## 创建一个消息循环  

```cpp
MSG msg;
while( GetMessage( &msg, nullptr, 0, 0) > 0){
    TranslateMessage( &msg );
    DispatchMessage( &msg );
}
```

解释如下：  
>Translates virtual-key messages into character messages.The character messages are posted to the calling thread's message queue, to be read the next time the thread calls the GetMessage or PeekMessage function.  
>将virtual-key转换为字符消息，在下一次读取消息时被读取出来
