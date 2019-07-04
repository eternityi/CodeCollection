# Key Value Coding

`KVC = Key Value Coding` 键值编码提供了一种使用字符串而不是访问器方法去访问一个对象实例变量的机制。

读取属性可以使用 `[someObject valueForKey:@"<key>"]`, 前提是对象定义了名为 &lt;key&gt; 或者 \_&lt;key&gt; 的成员变量, 或者声明了 -&lt;key&gt; 方法. 写入时使用\[someObject setValue:v forKey:@"&lt;key&gt;"\], 相应的对象需要声明了 -set&lt;Key&gt;: 方法. 如果对象中不存在 &lt;key&gt; 这个属性, 那么读取时会调用 someObject 的 -valueForUndefinedKey: 方法, 默认实现会抛出 NSUndefinedKeyException 异常. 写入不存在的属性时会相应的调用 －setValue:forUndefinedKey: 方法, 默认抛出相同的异常.  
keyPath 键值路径-valueForKeyPath: 和 -setValue:forKeyPath: 方法. KeyPath 的作用是, 如果还有属性是个类，则可以调用和设置该类的相关属性

```objectivec
Example Book/Author
main.m:#import<Foundation/Foundation.h>#import"Book.h"#import"Author.h"
intmain(intargc,constchar* argv[]){   Book*book0 = [[Bookalloc]init];   //1.通过KVC为属性赋值setValue: forKey:    [book0setValue:@"三重门"forKey:@"name"];   //2.若有setter，则调用setter赋值，若没有则直接赋值    [book0setValue:@"三联"forKey:@"publisher"];   //3.私有属性也可以访问Author*author0 = [[Authoralloc]init];    [book0setValue:author0forKey:@"author"];   //4.键值路径，为属性设置值setValue: forKeyPath:    [book0setValue:@"韩寒"forKeyPath:@"
author.name
"];   //5.KVC取值valueForKey:, valueForKeyPath:NSLog(@"book name: %@", [book0valueForKey:@"name"]);NSLog(@"author name: %@", [book0valueForKeyPath:@"
author.name
"]);   //6.KVC运算Book*book1 = [[Bookalloc]init];    [book1setName:@"西游记"];    [book1setPrice:22.5];Book*book2 = [[Bookalloc]init];    [book2setName:@"水浒传"];    [book2setPrice:23.5];Book*book3 = [[Bookalloc]init];    [book3setName:@"三国演艺"];    [book3setPrice:30];Book*book4 = [[Bookalloc]init];    [book4setName:@"红楼梦"];    [book4setPrice:25.0];   Author*author1 = [[Authoralloc]init];    [author1setValue:@"古人"forKey:@"name"];   NSArray*books =@[book1, book2, book3, book4];    [author1setValue:booksforKey:@"publishBooks"];   //获取所有书的价格,返回值是数组NSArray*prices = [author1valueForKeyPath:@"publishBooks.price"];NSLog(@"price: %@", prices);   //获取出版的书的个数,@count,返回值是NSNumberNSNumber*numberCount = [author1valueForKeyPath:@"publishBooks.@count"];NSLog(@"%@", numberCount);   //求价格的和NSNumber*priceSum = [author1valueForKeyPath:@"publishBooks.@sum.price"];NSLog(@"priceSum = %@", priceSum);   //求价格的平均值NSNumber*priceAverage = [author1valueForKeyPath:@"publishBooks.@avg.price"];NSLog(@"priceAverage = %@", priceAverage);   //求最大值最小值NSNumber*priceMax = [author1valueForKeyPath:@"publishBooks.@max.price"];NSLog(@"priceMax = %@", priceMax);   return0;}
Book类:@classAuthor;@interfaceBook :NSObject{NSString*_publisher;   @privateAuthor*_author;   }@property(nonatomic,copy)NSString*name;@property(nonatomic,assign)floatprice;- (void)setPublisher:(NSString*)publisher;@end
@implementationBook- (void)setPublisher:(NSString*)publisher{NSLog(@"Publisher is %@", publisher);_publisher= publisher;}@end
Author类:#import<Foundation/Foundation.h>
@interfaceAuthor :NSObject{@privateNSString*_name;NSArray*_publishBooks;}@end
@implementationAuthor@end
```

