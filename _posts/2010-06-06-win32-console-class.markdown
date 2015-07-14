---
layout: post
title:  "一个Win32控制台类"
date:  2010-06-06  10:54:00
categories: programming
---
对于编写Win32程序，尤其是游戏这种实时性高的程序来说，断点调试未必是最合适的调试方法。很多时候，我们更渴望看到实时打印的调试信息。

一种方法是使用VC的调试函数OutputDebugString()，将信息输出给VC调试控制台。还有一种方法是在Win32控制台工程的基础上创建窗口程序（入口为main而非WinMain）。可以用宏来控制编译，使Debug版本时有控制台，Release版本没有控制台，像这样：

{% highlight cpp %}
// 一个简单的方法
#ifndef _DEBUG
int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPSTR lpCmdLine, int nCmdShow) {
#else
    int main(int argc, char** argv) {
#endif//_DEBUG

{% endhighlight %}

但这种方法并不灵活，这里介绍一个直接用Win32 API获得控制台的方法。一切皆在代码中：

{% highlight cpp %}
// DbgCon.h

#ifndef DBGCON_H
#define DBGCON_H

#include <windows.h>;
#include <stdio.h>;

class DbgCon {
public:
    DbgCon();
    ~DbgCon();

    void    Show();
    void    Hide();

    void    Top();
    void    NoTop();

private:
    HWND    HWnd_;
    FILE*   ConIn_;
    FILE*   ConOut_;
};

#endif//DBGCON_H

{% endhighlight %}

{% highlight cpp %}
// DbgCon.cpp

#include "DbgCon.h"

DbgCon::DbgCon()
:   HWnd_(0)
,   ConIn_(0)
,   ConOut_(0) {
    // 申请一个控制台
    if( AllocConsole() ) {
        HWnd_ = GetConsoleWindow();
        // 把管道转入该控制台
        freopen_s(&ConIn_, "CONIN$", "r+t", stdin);
        freopen_s(&ConOut_, "CONOUT$", "w+t", stdout);
        // 去掉控制台窗口上的关闭功能
        //（控制台窗口上的关闭操作会关掉整个进程）
        HMENU HMenu = GetSystemMenu(HWnd_, FALSE);
        RemoveMenu(HMenu, SC_CLOSE, MF_BYCOMMAND);
    }
}

DbgCon::~DbgCon() {
    FreeConsole();
    if( ConOut_ ) {
        fclose(ConOut_);
    }
    if( ConIn_ ) {
        fclose(ConIn_);
    }
}

void DbgCon::Show() {
    // 显示控制台窗口（恢复到正常状态）
    ShowWindow(HWnd_, SW_SHOWNORMAL);
}

void DbgCon::Hide() {
    // 隐藏控制台窗口
    ShowWindow(HWnd_, SW_HIDE);
}

void DbgCon::Top() {
    // 置顶控制台窗口
    SetWindowPos(HWnd_, HWND_TOPMOST, 0, 0, 0, 0, SWP_NOMOVE||SWP_NOSIZE);
}

void DbgCon::NoTop() {
    // 取消置顶
    SetWindowPos(HWnd_, HWND_NOTOPMOST, 0, 0, 0, 0, SWP_NOMOVE||SWP_NOSIZE); 
}

{% endhighlight %}

