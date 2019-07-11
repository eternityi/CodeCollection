# iOS系统架构（一）

## iOS设备的CPU

#### 1. A系处理器与ARM指令集

苹果A系列处理器，使用的ARM架构处理器。

armv6、armv7、armv7s、arm64都是ARM处理器的指令集，所有指令集原则上都是向下兼容的。如 iPhone 4s 的CPU默认指令集为armv7指令集，但它可以同时也兼容armv6的指令集，只是在使用armv6的时候无法充分发挥其性能\(无法发挥armv7指令集中的新特性\)。

在macOS上使用Xcode进行开发，往往会使用iOS模拟器，但是macOS没有运行ARM指令集，编译运行的是x86\_64指令集\(或i386\)。模拟器的作用就是使用x86\_64架构去模拟iOS设备的ARM架构。

例如，真机的指令集有以下这些：  


* **armv6**: iPhone、iPhone 2、iPhone 3G、iPod Touch\(第一代\)、iPod Touch\(第二代\)       
* **armv7**: iPhone 3Gs、iPhone 4、iPhone 4s、iPad、iPad 2
* **armv7s**: iPhone 5、iPhone 5c \(静态库只要支持了armv7,就可以在armv7s的架构上运行\)
* **arm64**: iPhone 5s、iPhone 6、iPhone 6 Plus、iPhone 6s、iPhone 6s Plus、 iPhone 7 、iPhone 7 Plus、iPad Air、iPad Air2、iPad mini2、iPad mini3、iPad mini4、iPad Pro

#### **2. 在实际开发中的选择问题（Xcode设置）**

在Xcode中指令集相关选项（Build Setting中）

_**1）Architectures \(架构\)**_

指明选定的Target要求被编译生成的二进制包所支持的指令集，而支持的指令集越多，就会编译出包含多个指令集代码的数据包，对应生成二进制包就越大，也就是ipa包会变大。![](//upload-images.jianshu.io/upload_images/1856215-992c9ea4e4c3c88c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000)

![Xcode - Build Settings - Architectures](../.gitbook/assets/image%20%2813%29.png)

_**2）Valid Architectures \(有效架构\)**_

限制可能被支持的指令集的范围，也就是Xcode编译出来的二进制包类型最终从这些类型产生，而**编译出哪种指令集的包，将由Architectures与Valid Architectures（因此这个不能为空）的交集**来确定。

比如：

将Architectures支持arm指令集设置为：armv7, armv7s，

对应的Valid Architectures的支持的指令集设置为：armv7s, arm64，

那么此时Xcode生成二进制包所支持的指令集只有armv7s。

_**3）Build Active Architecture Only**_

指定是否只对当前连接设备所支持的指令集编译

设置为yes，是只编译当前的architecture版本，是为了编译速度更快，一般我们在debug环境下这样设置。

而设置为no时，系统会编译所有architecture下的版本。

所以，一般debug的时候可以选择设置为yes，release的时候要改为no，以适应不同设备。  


#### **3. 生成二进制包支持的指令集**

```text
第一种情况
Architectures:  armv7, armv7s, arm64
Valid  Architectures:  armv6, armv7s, arm64
生成支持的指令集是：arm64

第二种情况
Architectures:  armv6, armv7, armv7s
Valid  Architectures:  armv6, armv7s, arm64
生成支持的指令集是：armv7s

第三种情况
Architectures:  armv6, armv7
Valid  Architectures:  armv6, armv7s, arm64
生成支持的指令集是：armv7

第四种情况
Architectures:  armv6
Valid  Architectures:  armv6, armv7s, arm64
生成二进制包支持的指令集： 虽然编译成功了，但是并没有任何目标生成， 因为从XCode4.5开始，就不再支持armv6指令集，所以列表中写了也是白写。

第五种情况
Architectures:  armv7, armv7s, arm64
Valid  Architectures:  armv7，armv7s
生成二进制包支持的指令集： 编译出错信息
No architectures to compile for (ONLY_ACTIVE_ARCH=YES, active arch=arm64, VALID_ARCHS=armv7 armv7s)
```

从上面的情况可以看出，当Build Active Architecture Only起作用时：

连接的手机指令集匹配是由高到低（arm64 &gt; armv7s &gt; armv7）依次匹配的。

所以当连接的手机是指令集为arm64时，若Architectures列表为armv7, armv7s，则会选取armv7s指令集为目标指令集，如果此时Valid Architectures列表中包含该指令集，则成功生成的二进制包只支持armv7s指令集，若Valid Architectures列表不包含此指令集，则编译将会出错  No architectures to compile for \(ONLY\_ACTIVE\_ARCH=YES, active arch=arm64, VALID\_ARCHS=armv7 armv7s\)

_（使用很多静态库的时候，会遇到Undefined symbols for architecture arm\*\*\*\*的错误信息，其实就是静态库的包编译时候没有支持对应的指令集，所以可以重新编译，以生成需要的静态包）_

#### 4. 关于release

Release的App包需要去掉支持模拟器架构CPU指令集。

例如，环信就有这么一段：

> 由于 iOS 编译的特殊性，为了方便开发者使用，我们将 i386 x86\_64 armv7 arm64 几个平台都合并到了一起，所以使用动态库上传appstore时需要将i386 x86\_64两个平台删除后，才能正常提交审核 在SDK当前路径下执行以下命令删除i386 x86\_64两个平台 实时音视频版本Hyphenate.framework

```text
lipo Hyphenate.framework/Hyphenate -thin armv7 -output Hyphenate_armv7 
lipo Hyphenate.framework/Hyphenate -thin arm64 -output Hyphenate_arm64 
lipo -create Hyphenate_armv7 Hyphenate_arm64 -output Hyphenate
mv Hyphenate Hyphenate.framework/
```

## ARM指令集

先来看一下CPU的组成：

![CPU&#x7EC4;&#x6210;](../.gitbook/assets/image%20%282%29.png)

从上面的CPU结构图片中可以看出CPU主要分为**存储单元\(SU\)**和**运算单元\(ALU\)**以及**控制单元\(CU\)**。如果将这些部件和结构映射到**VCPU**这个类时你会发现：存储单元所对应的就是里面的数据成员；而运算单元和控制单元则对应里面的所有实例方法，运算单元提供了CPU指令的实现。

#### **1. 指令集**

**一个CPU里面所提供的所有的指令的集合称之为指令集。**

CPU指令集定义的是一个中央处理器所应该提供的基础功能的集合，它是一个标准是一个接口也是一个协议。在软件开发中具有协议和接口定义的概念，无论是消费者还是提供者都需要遵循这个标准来进行编程和交互：提供者要实现接口所具有的功能，至于如何实现则是内部的事情，不对外暴露，消费者也不需要知道具体的实现细节；消费者则总是要按接口提供的功能方法并组合使用来完成某种功能。

目前市面上有哪些主流的CPU指令集或CPU架构体系呢？

* x86/x64指令集

> 在iOS编程时如果要运行在模拟器上，代码生成的机器指令时就需要指定使用i386还是x64指令集，因为目前的mac电脑上基本采用了x86或者x64架构的CPU。

* ARM指令集

> 此处参考自:[https://baike.baidu.com/item/ARM/7518299](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2FARM%2F7518299)

ARM处理器是英国Acorn有限公司设计的低功耗成本的第一款[RISC](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2FRISC%2F62696)微处理器。全称为Advanced RISC Machine。ARM处理器本身是32位设计，但也配备16位指令集。

> 目前市面上的主流智能手机等移动设备配备的CPU都采用ARM架构。iOS应用真机编译出来的机器指令都是ARM指令，因此需要在编译时指定armv7或者arm64指令集。

* MIPS架构

MIPS架构（英语：MIPS architecture，为Microprocessor without interlocked piped stages architecture的缩写），是一种采取精简指令集（RISC）的处理器架构。

> 目前国内的龙芯CPU，采用的就是MIPS指令集。

* POWER -PC

POWER-PC由摩托罗拉公司和苹果公司联合开发的高性能32位和64位RISC微处理器系列，以与垄断PC机市场的Intel微处理器和微软公司的软件相竞争。PowerPC微处理器1994年推出。

> 您是否在很多iOS库的头文件里面看到过POWER-PC的宏定义，早期的苹果电脑都用POWER-PC的CPU，现在苹果电脑基本都改为x64架构的CPU了。

## Reference:

[深入iOS系统底层之指令集](https://www.jianshu.com/p/54884ce976ca)



