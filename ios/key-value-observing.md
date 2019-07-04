# key Value Observing



KVO: key value observing观察者模式所有满足 KVC 条件的对象都可以使用. 当对象的某个属性发生变化时, 会向相关的观察者发送通知, 告知哪个属性变化了, 从什么值变成什么. 甚至可以在值发生变化之前得到通知.1.**为对象的属性注册观察者**：对象通过调用下面这个方法为属性添加观察者: - \(void\)addObserver:\(NSObject \*\)observer              forKeyPath:\(NSString \*\)keyPath                    options:\(NSKeyValueObservingOptions\)options                     context:\(void \*\)context  


* observer: 观察者对象. 其必须实现方法observeValueForKeyPath:ofObject:change:context:.
* keyPath: 被观察的属性，其不能为nil.
* options: 设定通知观察者时传递的属性值，是传改变前的呢，还是改变后的，通常设置为NSKeyValueObservingOptionNew
* context: 一些其他的需要传递给观察者的上下文信息，通常设置为nil

2.**观察者接收通知**，并做出处理，观察者通过实现下面的方法，完成对属性改变的响应:- \(void\)observeValueForKeyPath:\(NSString \*\)keyPath                                      ofObject:\(id\)object                                        change:\(NSDictionary \*\)change                                         context:\(void \*\)context

* keyPath: 被观察的属性，其不能为nil.
* object: 被观察者的对象.
* change: 属性值，根据上面提到的Options设置，给出对应的属性值
* context: 上面传递的context对象。

3.**清除观察者:**对象通过下面这个方法移除观察者： - \(void\)removeObserver:\(NSObject \*\)anObserver forKeyPath:\(NSString \*\)keyPath  
**4.其他：** 

* 使用KVO消息传递机制有两个要求：（1\)观察者必须知道被观察对象，即在同一作用域。（2）观察者还需要知道被观察对象的生命周期，因为在销毁发送者对象之前，需要取消观察者的注册。
* 当一个观察者观察多个对象的相同属性（即不同Object，但是KeyPath相同），可通过设定静态的Context变量来区分不同的通知。

