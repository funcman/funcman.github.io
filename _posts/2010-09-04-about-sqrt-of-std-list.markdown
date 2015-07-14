---
layout: post
title:  "关于std::list的排序"
date:  2010-09-04  12:58:00
categories: programming
---
std::list和std::vector不同，无法使用STL算法库里的sort()，因为std::list不支持随机访问（random access）。

{% highlight cpp %}
// std::list排序示例

#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <list>

// 它是个仿函数
struct mycmp {
    bool operator()(int* a, int* b) {
        return *a < *b;
    }
}; 

int main() {
    std::list il;
    int ia[10];

    time_t t;
    time(&t);
    srand((unsigned int)t);

    printf("Before: ");
    for( int i=0; i<10; ++i ) {
        ia[i] = rand();
        il.push_back(&ia[i]);
        printf("%d ", ia[i]);
    }
    printf("\n");

    il.sort(mycmp());

    printf("After: ");
    std::list::iterator i;
    for( i=il.begin(); i!=il.end(); ++i ) {
        printf("%d ", **i);
    }
    printf("\n");

    return 0;
}

{% endhighlight %}

