# iOS多线程编程（二）底层线程操作

在这篇文章中，我将为你整理一下 iOS 开发中几种多线程方案，以及其使用方法和注意事项。当然也会给出几种多线程的案例，在实际使用中感受它们的区别。还有一点需要说明的是，这篇文章将会使用 `Swift`和 `Objective-c`两种语言讲解，双语幼儿园。OK，let's begin!

### 概述

这篇文章中，我不会说多线程是什么、线程和进程的区别、多线程有什么用，当然我也不会说什么是串行、什么是并行等问题，这些我们应该都知道的。

在 iOS 中其实目前有 `4`套多线程方案，他们分别是：

> * Pthreads
> * NSThread
> * GCD
> * NSOperation & NSOperationQueue

所以接下来，我会一一讲解这些方案的使用方法和一些案例。在将这些内容的时候，我也会顺带说一些多线程周边产品。比如： **线程同步**、 **延时执行**、 **单例模式**等等。

### Pthreads

其实这个方案不用说的，只是拿来充个数，为了让大家了解一下就好了。百度百科里是这么说的：

> POSIX线程（POSIX threads），简称Pthreads，是线程的POSIX标准。该标准定义了创建和操纵线程的一整套API。在类Unix操作系统（Unix、Linux、Mac OS X等）中，都使用Pthreads作为操作系统的线程。

简单地说，这是一套在很多操作系统上都通用的多线程API，所以移植性很强（然并卵），当然在 iOS 中也是可以的。不过这是基于 **c语言**的框架，使用起来这酸爽！感受一下：

**OBJECTIVE-C**

当然第一步要包含头文件

> \#import \<pthread.h>

然后创建线程，并执行任务

```
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    pthread_t thread;
    //创建一个线程并自动执行
    pthread_create(&thread, NULL, start, NULL);
}

void *start(void *data) {
    NSLog(@"%@", [NSThread currentThread]);

    return NULL;
}
```

打印输出：

> 2015-07-27 23:57:21.689 testThread\[10616:2644653] \<NSThread: 0x7fbb48d33690>{number = 2, name = (null)}

看代码就会发现他需要 **c语言函数**，这是比较蛋疼的，更蛋疼的是你需要手动处理线程的各个状态的转换即管理生命周期，比如，这段代码虽然创建了一个线程，但并没有销毁。

**SWIFT**

很遗憾，在我目前的 `swift1.2`中无法执行这套方法，原因是这个函数需要传入一个函数指针 `CFunctionPointer<T>`类型，但是目前 swift 无法将方法转换成此类型。听说 `swift 2.0`引入一个新特性 `@convention(c)`, 可以完成 Swift 方法转换成 c 语言指针的。[在这里可以看到](https://link.jianshu.com/?t=http://stackoverflow.com/questions/25514176/using-swift-cfunctionpointer-to-pass-a-callback-to-coremidi-api)

那么，`Pthreads`方案的多线程我就介绍这么多，毕竟做 iOS 开发几乎不可能用到。但是如果你感兴趣的话，或者说想要自己实现一套多线程方案，从底层开始定制，那么可以去搜一下相关资料。\
\
