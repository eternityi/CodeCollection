# iOS多线程编程（一）多线程基础

## 多线程基础

#### 1. 进程

> 进程是指在系统中正在运行的一个应用程序  
> 每个进程之间是独立的，每个进程均运行在其专用且受保护的内存空间内

#### 2.线程

> 1个进程要想执行任务，必须得有线程（每1个进程至少要有1条线程，称为主线程）  
> 一个进程（程序）的所有任务都在线程中执行

#### 3. 进程和线程的比较

> 1.线程是CPU调用\(执行任务\)的最小单位。  
> 2.进程是CPU分配资源的最小单位。  
> 3.一个进程中至少要有一个线程。  
> 4.同一个进程内的线程共享进程的资源。

#### 4. 线程的串行

> 1个线程中任务的执行是串行的  
> 如果要在1个线程中执行多个任务，那么只能一个一个地按顺序执行这些任务  
> 也就是说，在同一时间内，1个线程只能执行1个任务

#### 5. 多线程

> 1个进程中可以开启多条线程，每条线程可以并行（同时）执行不同的任务  
> 多线程技术可以提高程序的执行效率

#### 6. 多线程原理

> **同一时间，CPU只能处理1条线程，只有1条线程在工作（执行）**，多线程并发（同时）执行，其实是**CPU快速地在多条线程之间调度（切换）**，如果CPU调度线程的时间足够快，就造成了多线程并发执行的假象。  
> 那么如果线程非常非常多，会发生什么情况？  
> CPU会在N多线程之间调度，CPU会累死，消耗大量的CPU资源，同时每条线程被调度执行的频次也会会降低（线程的执行效率降低）。  
> 因此我们一般只开3-5条线程。

#### 7. 多线程优缺点

> **多线程的优点**  
> 能适当提高程序的执行效率  
> 能适当提高资源利用率（CPU、内存利用率）  
> **多线程的缺点**  
> 创建线程是有开销的，iOS下主要成本包括：内核数据结构（大约1KB）、栈空间（子线程512KB、主线程1MB，也可以使用-setStackSize:设置，但必须是4K的倍数，而且最小是16K），创建线程大约需要90毫秒的创建时间  
> 如果开启大量的线程，会降低程序的性能，线程越多，CPU在调度线程上的开销就越大。  
> 程序设计更加复杂：比如线程之间的通信、多线程的数据共享等问题。

#### 8. 多线程的应用

> **主线程的主要作用**  
> 显示\刷新UI界面  
> 处理UI事件（比如点击事件、滚动事件、拖拽事件等）  
> **主线程的使用注意**  
> 别将比较耗时的操作放到主线程中  
> 耗时操作会卡住主线程，严重影响UI的流畅度，给用户一种“卡”的坏体验  
> **将耗时操作放在子线程中执行，提高程序的执行效率**



