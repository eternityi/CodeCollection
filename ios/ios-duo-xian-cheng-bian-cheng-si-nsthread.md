# iOS多线程编程（三）NSThread

### NSThread

这套方案是经过苹果封装后的，并且完全面向对象的。所以你可以直接操控线程对象，非常直观和方便。但是，它的生命周期还是需要我们手动管理，所以这套方案也是偶尔用用，比如 `[NSThread currentThread]`，它可以获取当前线程类，你就可以知道当前线程的各种属性，用于调试十分方便。下面来看看它的一些用法。

#### 创建并启动

* 先创建线程类，再启动

  **OBJECTIVE-C**

  ```text
    // 创建
    NSThread *thread = [[NSThread alloc] initWithTarget:self selector:@selector(run:) object:nil];

    // 启动
    [thread start];
  ```

  **SWIFT**

  ```text
    //创建
    let thread = NSThread(target: self, selector: "run:", object: nil)

    //启动
    thread.start()
  ```

* 创建并自动启动

  **OBJECTIVE-C**

  ```text
    [NSThread detachNewThreadSelector:@selector(run:) toTarget:self withObject:nil];
  ```

  **SWIFT**

  ```text
    NSThread.detachNewThreadSelector("run:", toTarget: self, withObject: nil)
  ```

* 使用 NSObject 的方法创建并自动启动

  **OBJECTIVE-C**

  ```text
    [self performSelectorInBackground:@selector(run:) withObject:nil];
  ```

  **SWIFT**

  很遗憾 too! 苹果认为 `performSelector:`不安全，所以在 Swift 去掉了这个方法。

  > Note: The performSelector: method and related selector-invoking methods are not imported in Swift because they are inherently unsafe.

#### 其他方法

除了创建启动外，NSThread 还以很多方法，下面我列举一些常见的方法，当然我列举的并不完整，更多方法大家可以去类的定义里去看。

**OBJECTIVE-C**

```text
//取消线程
- (void)cancel;

//启动线程
- (void)start;

//判断某个线程的状态的属性
@property (readonly, getter=isExecuting) BOOL executing;
@property (readonly, getter=isFinished) BOOL finished;
@property (readonly, getter=isCancelled) BOOL cancelled;

//设置和获取线程名字
-(void)setName:(NSString *)n;
-(NSString *)name;

//获取当前线程信息
+ (NSThread *)currentThread;

//获取主线程信息
+ (NSThread *)mainThread;

//使当前线程暂停一段时间，或者暂停到某个时刻
+ (void)sleepForTimeInterval:(NSTimeInterval)time;
+ (void)sleepUntilDate:(NSDate *)date;
```

**SWIFT**

Swift的方法名字和OC的方法名都一样，我就不浪费空间列举出来了。

其实，NSThread 用起来也挺简单的，因为它就那几种方法。同时，我们也只有在一些非常简单的场景才会用 NSThread, 毕竟它还不够智能，不能优雅地处理多线程中的其他高级概念。所以接下来要说的内容才是重点。  


