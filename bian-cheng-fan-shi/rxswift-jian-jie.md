# RxSwift 简介

在 iOS 开发过程中，我们几乎无时无刻都要面对异步事件的处理。例如，按键点击、数据保存、、音频后台播放、交互动画展示。这些事件并不具备特定时序性，甚至它们可能同时发生。

虽然 Apple 提供了**通知、代理、GCD、闭包**等异步机制，但是这些机制缺乏一个统一的抽象表述。另外，这些机制在处理共享的可变数据或状态时不够清晰简练。当然，这并不是说编写优雅的异步代码不现实。毕竟与其他平台相比 iOS 的异步机制还是很强大的。

幸运的是，我们能够通过 RxSwift 优雅的处理异步代码。

## RxSwift 简介

其实响应式编程并不是一个什么新的概念，只不过是最近几年受到了开发者更多的关注。它最早由巨硬提出，主要的目的是为了应对复杂的 UI 异步事件和应用实时响应。社区中也已经有了各种语言版本的响应式编程实现，包括：RxJS、RxKotlin、Rx.NET、RxScala、RxSwift。这些类库仅仅只是实现方式存在差异，所以开发者在讨论应用逻辑时不会存在沟通障碍。

RxSwift 作为 Swift 语言的响应式编程实现，它在传统的命令式编程和纯函数式编程中找到了一个很好的平衡点。通过使用不可变代码定义异步处理输入，RxSwift 以一种确定可组合的形式对事件做出响应。

总的来说，RxSwift 有三个主要构成部分：**Observable**、**Operator**、**Scheduler**。下面我们就来一一介绍。

#### Observable

Observable 类可以说是 RxSwift 整个框架的基石。它能异步的触发一系列事件流并携带不可更改的状态变量。简单来说就是：它能让某个类的实例在一段时间内实现对另一个实例对象值的观察。例如：观察者可以捕获对所有可观察对象触发的事件，从而实现 UI 的实时更新或者是数据的实时处理。

其中 Observable 类遵循了 ObservableType 协议。另外，Observable 对象所能触发的事件只有以下三种：

1. _next_ 事件：该事件在触发时会将可观察对象的最新值传递给观察者。
2. _completed_ 事件：该事件意味着可观察对象的生命周期正常结束不会在继续触发事件。
3. _error_ 事件：该事件表明可观察对象出现了错误导致生命周期异常终止。

对于一个可观察的整型变量来说，异步环境下它所触发的事件可以在时间线上被描绘成这样一个事件序列：

![](../.gitbook/assets/image%20%284%29.png)

另外，我们可以对这三类事件进行组合从而实现更为复杂的业务逻辑。与此同时，我们还可以使用该机制轻松实现代码解耦和多个对象间数据传递，无需编写代理或者闭包代码。

![](../.gitbook/assets/image%20%285%29.png)

这里，我们还有一点值得注意。那就是可观察序列其实有两种类型。

#### 有限观察序列（ Finite observable sequences ）

该序列是指那些最后会以 _completed_ 或者 _error_ 事件终极生命周期的可观察对象。最典型的例子就是，通过 API 进行网络请求：

1. 开始数据请求并准备进行数据接收。
2. 接收到服务端响应开始接收数据。
3. 如果服务器或者网络发生故障则关闭请求并触发错误处理。
4. 如果一切正常则对请求数据进行处理和分析。

下面是一个文件下载请求的 Rx 范式的代码：

```swift
API.download(file: "http://www...")
    .subscribe( onNext: { data in
                        append data to temporary file }, 
                onError: { error in
                        display error to user }, 
                onCompleted: {
                        use downloaded file })
```

这段代码中 _API.download \(file:\)_ 函数会创建一个 Observable 实例对象，并且在整个数据接收过程中会不断的触发 _next_ 事件。然后，我们在 _next_ 事件中会将这些片段数据保存到临时文件中。如果此过程出现错误的话，我们会将错误信息展示给用户。如果一切顺利我们会将临时文件保存到设备中。最后在下载完成后，我们可以在 _completed_ 进行下一步的逻辑处理。

#### 无限观察序列（ Infinite observable sequences ）

与网络任务不同的是，UI 以及交互事件是无限观察序列。它们并不存在一个明确的生命周期终结点。例如，针对可能的设备方向旋转，我们需要实时进行布局修改。而设备的方向旋转本身是随机发生的并且与应用本身具有同样的生命周期。因此 Rx 也需要一种机制支持这种无限观察序列。

针对这种情况，在 RxSwift 中我们可以通过以下代码来应对：

```swift
UIDevice.rx.orientation.subscribe(onNext: { current in 
    switch current { 
        case .landscape:
            re-arrange UI for landscape 
        case .portrait:
            re-arrange UI for portrait 
    } 
})
```

### 操作符

ObservableType 以及 Observable 类的实现中都包含大量的异步处理方法，这些方法也被称为操作符。由于这些操作符只是进行异步输入处理并产生对应输出，所以它并不会对应用产生多余的副作用。另外，因为操作符之间的高度解耦所以我们很容易对它进行组合以期实现复杂的功能。

例如，对于上面的设备方向旋转，我们可以对所有的情况进行过滤然后对部分值进行进一步处理。

```swift
UIDevice.rx.orientation
    .filter { value in
        return value != .landscape 
    } 
    .map { _ in
        return "Portrait is the best!" 
    } 
    .subscribe( onNext: { string in 
        showAlert(text: string) 
    })
```

上面的代码中，我们首先会将所有 _.landscape_ 方向过滤掉不做任何处理。然后，我们再将剩下的 _portrait_ 转化为字符串 _Portrait is the best!_ 。整个处理流程大致如下：

![](https://bignerdcoding.com/usr/uploads/2017/09/1787055755.png)

这种函数式的操作符让我们可以灵活的组合出更强大的功能。

### Scheduler

Schedulers 是一个与 GCD 相对应的概念，只不过前者使用起来更为方便。RxSwift 中预定义的 Schedulers 足够开发者应对绝大多数的编程场景。

例如，我们可以使用串型序列 _SerialDispatchQueueScheduler_ 来处理 _next_ 事件，通过 _ConcurrentDispatchQueueScheduler_ 运行并行文件下载任务，通过 _OperationQueueScheduler_ 运行一个 _NSOperationQueue_ 操作队列。甚至你可以在同一个观察对象的不同任务中使用不同的 Schedulers 类型，如下图：

![](https://bignerdcoding.com/usr/uploads/2017/09/4255488552.png)

我们将左侧的任务用不同的颜色加以区分，然后在右侧任务被拆分为不同的步骤并且放在不同 Schedulers 中。例如，_network subscription_ 任务就被拆分为三个步骤并依次放入了 _Custom NSOperation Scheduler_ 、_Background Concurrent Scheduler_、_Main Thred Serial Scheduler_ 。

### 补充

值得注意的是， RxSwift 并没有对客户端的应用架构作出硬性规定。这意味着，我们可以在已有项目中引入 RxSwift 进行响应式编程实践。当然已有框架中必定存在一个最适合 RxSwift 的，而它就是 MVVM。因为在 MVVM 中我们可以将 VM 中的部分属性直接与 UI 进行绑定。

![](https://bignerdcoding.com/usr/uploads/2017/09/242786880.png)

另外，对于 iOS 编程来说仅仅有 RxSwift 是远远不够的。RxSwift 只是 Swift 语言的响应式实现，我们还需要一种 Cocoa 层面的实现。好在这里存在 RxCocoa 作为 UIKit 的响应式补充。前面设备方向代码 _UIDevice.rx.orientation_ 就是 RxCocoa 的应用 。

### 总结

作为系列开篇，本文介绍了 RxSwift 的一些基本理念和构成，更多相关的内容将会在后面带来。

