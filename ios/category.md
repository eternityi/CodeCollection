# Objective-C语言基础（Category）

## 分类（Category）

#### 概念

分类（Category）是OC中的特有语法，可以给基类添加一些功能，更加灵活的添加我们想要的功能。它是表示一个指向分类的结构体的指针。

例如：

```objectivec
//  Person+MyCategory.h
#import "Person.h"

@interface Person (MyCategory)

-(void)read;

@end
```

```objectivec
//  Person+MyCategory.m
#import "Person+MyCategory.h"

@implementation Person (MyCategory)

-(void)read{
    NSLog(@"调用了MyCategory的read方法！");
}

@end
```

#### 使用

原则上它只能增加方法，不能增加成员（实例）变量。

1. 分类中可以添加实例方法、类方法和协议，可以使用@property添加属性，也可以通过关联对象添加实例变量。
2. 分类中的可以写@property, 但不会生成`setter/getter`方法, 也不会生成实现以及私有的成员变量（编译时会报警告）;
3. 可以在分类中访问原有类中.h中的属性;
4. 如果分类中有和原有类同名的方法, 会优先调用分类中的方法, 就是说会忽略原有类的方法。所以同名方法调用的优先级为 `分类 > 本类 > 父类`。因此在开发中尽量不要覆盖原有类;
5. 如果多个分类中都有和原有类中同名的方法, 那么调用该方法的时候执行谁由编译器决定；编译器会执行最后一个参与编译的分类中的方法。

#### 应用场景

* 声明私有方法
* 分解类的定义和实现文件
* 将Framework的私有方法公开

#### 本质

通过查看分类的源码我们可以找到category\_t 结构体。

```c
struct category_t {
    const char *name;
    classref_t cls;
    struct method_list_t *instanceMethods; // 对象方法
    struct method_list_t *classMethods; // 类方法
    struct protocol_list_t *protocols; // 协议
    struct property_list_t *instanceProperties; // 属性
    // Fields below this point are not always present on disk.
    struct property_list_t *_classProperties;

    method_list_t *methodsForMeta(bool isMeta) {
        if (isMeta) return classMethods;
        else return instanceMethods;
    }

    property_list_t *propertiesForMeta(bool isMeta, struct header_info *hi);
};
```

从源码基本可以看出我们平时使用categroy的方式，**对象方法，类方法，协议，和属性**都可以找到对应的存储方式。并且我们发现**分类结构体中是不存在成员变量**的，因此分类中是不允许添加成员变量的。**分类中添加的属性并不会帮助我们自动生成成员变量，只会生成get set方法的声明，需要我们自己去实现**。

通过源码我们发现，分类的方法，协议，属性等好像确实是存放在categroy结构体里面的，那么他又是如何存储在类对象中的呢？ 我们来看一下底层的内部方法探寻其中的原理。 首先我们通过命令行将Preson+Test.m文件转化为c++文件，查看其中的编译过程。

```
xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc Category+Test.m
```

[ref：](https://juejin.im/post/5aef0a3b518825670f7bc0f3)\
\


#### 常见问题

**1.关联对象添加成员变量**

我们知道在一个类中用@property声明属性，编译器会自动帮我们生成`_成员变量`和`setter/getter`，但分类的指针结构体中，根本没有属性列表。所以在分类中用@property声明属性，既无法生成`_成员变量`也无法生成`setter/getter`。\
因此结论是：我们可以用@property声明属性，编译和运行都会通过，只要不使用程序也不会崩溃。但如果调用了`_成员变量`和`setter/getter`方法，报错就在所难免了。\
既然报错的根本原因是使用了系统没有生成的`setter/getter`方法，可不可以在手动添加`setter/getter`来避免崩溃，完成调用呢？\
其实是可以的。由于OC是动态语言，方法真正的实现是通过`runtime`完成的，虽然系统不给我们生成`setter/getter`，但我们可以通过`runtime`手动添加`setter/getter`方法。

实现如下:

```
#import <objc/runtime.h>

static NSString *nameWithSetterGetterKey = @"nameWithSetterGetterKey";   //定义一个key值
@implementation Programmer (Category)

//运行时实现setter方法
- (void)setNameWithSetterGetter:(NSString *)nameWithSetterGetter {
        objc_setAssociatedObject(self, &nameWithSetterGetterKey, nameWithSetterGetter, OBJC_ASSOCIATION_COPY);
}

//运行时实现getter方法
- (NSString *)nameWithSetterGetter {
    return objc_getAssociatedObject(self, &nameWithSetterGetterKey);
}

@end
```

**但是注意，以上代码仅仅是手动实现了**`setter/getter`**方法，但调用**`_成员变量`**依然报错。**

_问：Category的实现原理，以及Category为什么只能加方法不能加属性?_

答：分类的实现原理是将category中的方法，属性，协议数据放在category\_t结构体中，然后将结构体内的方法列表拷贝到类对象的方法列表中。 Category可以添加属性，但是并不会自动生成成员变量及set/get方法。因为category\_t结构体中并不存在成员变量。通过之前对对象的分析我们知道成员变量是存放在实例对象中的，并且编译的那一刻就已经决定好了。而分类是在运行时才去加载的。那么我们就无法再程序运行时将分类的成员变量中添加到实例对象的结构体中。因此分类中不可以添加成员变量。\
\


### 扩展（Class Extension）

Extension是Category的一个特例。类扩展与分类相比只少了分类的名称，所以称之为“匿名分类”。\
其实开发当中，我们几乎天天在使用。对于有些人来说像是最熟悉的陌生人。

```
@interface XXX ()
//私有属性
//私有方法（如果不实现，编译时会报警,Method definition for 'XXX' not found）
@end复制代码
```

#### 作用：

为一个类添加额外的原来没有变量，方法和属性\
一般的类扩展写到`.m`文件中\
一般的私有属性写到`.m`文件中的类扩展中\


### 分类与扩展的区别：

1. 类别中原则上只能增加方法（能添加属性的的原因只是通过`runtime`解决无`setter/getter`的问题而已）；
2. 类扩展不仅可以增加方法，还可以增加实例变量（或者属性），只是该实例变量默认是@private类型的（
3. 用范围只能在自身类，而不是子类或其他地方）；
4. 类扩展中声明的方法没被实现，编译器会报警，但是类别中的方法没被实现编译器是不会有任何警告的。这是因为**类扩展是在编译阶段被添加到类中，而类别是在运行时添加到类中**。
5. 类扩展不能像类别那样拥有独立的实现部分（@implementation部分），也就是说，类扩展所声明的方法必须依托对应类的实现部分来实现。
6. 定义在 .m 文件中的类扩展方法为私有的，定义在 .h 文件（头文件）中的类扩展方法为公有的。类扩展是在 .m 文件中声明私有方法的非常好的方式。



扩展

* 在编译器决议，是类的一部分，在编译器和头文件的@interface和实现文件里的@implement一起形成了一个完整的类。
* 伴随着类的产生而产生，也随着类的消失而消失。
* Extension一般用来隐藏类的私有消息，你必须有一个类的源码才能添加一个类的Extension，所以对于系统一些类，如NSString，就无法添加类扩展

分类

* 是运行期决议的
* 类扩展可以添加实例变量，分类不能添加实例变量
* 原因：因为在运行期，对象的内存布局已经确定，如果添加实例变量会破坏类的内部布局，这对编译性语言是灾难性的。
