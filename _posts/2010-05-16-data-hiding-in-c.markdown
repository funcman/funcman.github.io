---
layout: post
title:  "C语言对数据的隐藏封装"
date:  2010-05-16  19:48:00
categories: programming
---
在C++中，如果我们想把数据结构隐藏起来，只为用户提供接口，可以通过实现继承接口类的方式到达目的。那C中，可以采用什么方式呢。下面有一例：

{% highlight c %}
// demo.h

#ifndef DEMO_H
#define DEMO_H

typedef struct Demo* Demo;

extern Demo DemoCreate(int num);
extern void DemoDestory(Demo demo);
extern void DemoShow(Demo demo);

#endif//DEMO_H

{% endhighlight %}

{% highlight c %}
// demo.c

#include "demo.h"
#include <stdio.h>
#include <stdlib.h>

typedef struct Demo_ {
    int num_;
} Demo_;

Demo DemoCreate(int num) {
    Demo_* demo = (Demo_*)malloc(sizeof(Demo_));
    demo->num_ = num;
    return (Demo)demo;
}

void DemoDestory(Demo demo) {
    free(demo);
}

void DemoShow(Demo demo) {
    printf("%d\n", ((Demo_*)demo)->num_);
}

{% endhighlight %}

{% highlight c %}
// main.c

#include "demo.h"

int main() {
    Demo demo = DemoCreate(777);
    DemoShow(demo);
    DemoDestory(demo);
    return 0;
}

{% endhighlight %}
