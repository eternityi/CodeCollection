# Allocation and Initialization

It takes two steps to create an object in Objective-C. You must both:

* Dynamically allocate memory for the new object, and
* Initialize the newly allocated memory to appropriate values.

An object isn't fully functional until both steps have been completed. As discussed in  [Chapter 3](http://www.foldr.org/~michaelw/objective-c/ObjectiveC/3CoreObjC/iThe_Objective_C_Language.html), each step is accomplished by a separate method, but typically in a single line of code:

> ```text
> id anObject = [[Rectangle alloc] init];
> ```

Separating allocation from initialization gives you individual control over each step so that each can be modified independently of the other. The following sections look first at allocation and then at initialization, and discuss how they are in fact controlled and modified.  


#### Allocating Memory For Objects

In Objective-C, memory for new objects is allocated using class methods defined in the NSObject class. NSObject defines two principal methods for this purpose, `alloc` and `allocWithZone:`.

> ```text
> + (id)alloc;
> + (id)allocWithZone:(NSZone *)zone;
> ```

These methods allocate enough memory to hold all the instance variables for an object belonging to the receiving class. They don't need to be overridden and modified in subclasses.  


#### Initializing New Objects

The `alloc` and `allocWithZone:` methods initialize a new object's `isa` instance variable so that it points to the object's class \(the class object\). All other instance variables are set to 0. Usually, an object needs to be more specifically initialized before it can be safely used.

This initialization is the responsibility of class-specific instance methods that, by convention, begin with the abbreviation "init". If the method takes no arguments, the method name is just those four letters, `init`. If it takes arguments, labels for the arguments follow the "init" prefix. For example, an NSView can be initialized with an `initWithFrame:` method.

Every class that declares instance variables must provide an `init...` method to initialize them. The NSObject class declares the `isa` variable and defines an `init` method. However, since `isa` is initialized when memory for a new object is allocated, all NSObject's `init` method does is return `self`. NSObject declares the method mainly to establish the naming convention described above.  


**The Returned Object**

An `init...` method normally initializes the instance variables of the receiver, then returns it. It's the responsibility of the method to return an object that can be used without error.

However, in some cases, this responsibility can mean returning a different object than the receiver. For example, if a class keeps a list of named objects, it might provide an `initWithName:` method to initialize new instances. If there can be no more than one object per name, `initWithName:` might refuse to assign the same name to two objects. When asked to assign a new instance a name that's already being used by another object, it might free the newly allocated instance and return the other object-thus ensuring the uniqueness of the name while at the same time providing what was asked for, an instance with the requested name.

In a few cases, it might be impossible for an `init...` method to do what it's asked to do. For example, an `initFromFile:` method might get the data it needs from a file passed as an argument. If the file name it's passed doesn't correspond to an actual file, it won't be able to complete the initialization. In such a case, the `init...` method could free the receiver and return `nil`, indicating that the requested object can't be created.

Because an `init...` method might return an object other than the newly allocated receiver, or even return `nil`, it's important that programs use the value returned by the initialization method, not just that returned by `alloc` or `allocWithZone:`. The following code is very dangerous, since it ignores the return of `init`.

> ```text
> id anObject = [SomeClass alloc];
> [anObject init];
> [anObject someOtherMessage];
> ```

It's recommended that you combine allocation and initialization messages:

> ```text
> id anObject = [[SomeClass alloc] init];
> [anObject someOtherMessage];
> ```

If there's a chance that the `init...` method might return `nil`, the return value should be checked before proceeding:

> ```text
> id anObject = [[SomeClass alloc] init];
> if ( anObject )
>     [anObject someOtherMessage];
> else
>     . . .
> ```

**Arguments**

An `init...` method must ensure that all of an object's instance variables have reasonable values. This doesn't mean that it needs to provide an argument for each variable. It can set some to default values or depend on the fact that \(except for `isa`\) all bits of memory allocated for a new object are set to 0. For example, if a class requires its instances to have a name and a data source, it might provide an `initWithName:fromFile:` method, but set nonessential instance variables to arbitrary values or allow them to have the null values set by default. It could then rely on methods like `setEnabled:`, `setFriend:`, and `setDimensions:` to modify default values after the initialization phase had been completed.

Any `init...` method that takes arguments must be prepared to handle cases where an inappropriate value is passed.  


**Coordinating Classes**

Every class that declares instance variables must provide an `init...` method to initialize them \(unless the variables require no initialization\). The `init...` methods the class defines initialize only those variables declared in the class. Inherited instance variables are initialized by sending a message to `super` to perform an initialization method defined somewhere farther up the inheritance hierarchy:

> ```text
> - initWithName:(char *)string
> {
>     if ( self = [super init] ) {
>         name = (char *)NSZoneMalloc([self zone], 
>             strlen(string) + 1);
>         strcpy(name, string);
>         return self;
>     }
>     return nil;
> }
> ```

The message to `super` chains together initialization methods in all inherited classes. Because it comes first, it ensures that superclass variables are initialized before those declared in subclasses. For example, a Rectangle object must be initialized as an NSObject, a Graphic, and a Shape before it's initialized as a Rectangle. \(See  [Figure 3-2 ](http://www.foldr.org/~michaelw/objective-c/ObjectiveC/3CoreObjC/iClasses.html)for the Rectangle inheritance hierarchy.\)

The connection between the `initWithName:` method illustrated above and the inherited `init` method it incorporates is diagrammed in the figure below:

**Figure 5-1 Incorporating an Inherited Initialization Method**![\[image: ../Art/InitSimple.gif\]](http://www.foldr.org/~michaelw/objective-c/ObjectiveC/Art/InitSimple.gif)

A class must also make sure that all inherited initialization methods work. For example, if class A defines an `init` method and its subclass B defines an `initWithName:` method, as shown in the figure above, B must also make sure that an `init` message will successfully initialize B instances. The easiest way to do that is to replace the inherited `init` method with a version that invokes `initWithName:`.

> ```text
> - init
> {
>     return [self initWithName:"default"];
> }
> ```

The `initWithName:` method would, in turn, invoke the inherited method, as was shown in the example and figure above. That figure can be modified to include B's version of `init`, as shown below:

**Figure 5-2 Covering an Inherited Initialization Model**![\[image: ../Art/InitA\_B.gif\]](http://www.foldr.org/~michaelw/objective-c/ObjectiveC/Art/InitA_B.gif)

Covering inherited initialization methods makes the class you define more portable to other applications. If you leave an inherited method uncovered, someone else may use it to produce incorrectly initialized instances of your class.  


**The Designated Initializer**

In the example above, `initWithName:` would be the **designated initializer** for its class \(class B\). The designated initializer is the method in each class that guarantees inherited instance variables are initialized \(by sending a message to `super` to perform an inherited method\). It's also the method that does most of the work, and the one that other initialization methods in the same class invoke. It's a Cocoa convention that the designated initializer is always the method that allows the most freedom to determine the character of a new instance \(usually this is the one with the most arguments, but not always\).

It's important to know the designated initializer when defining a subclass. For example, suppose we define class C, a subclass of B, and implement an `initWithName:fromFile:` method. In addition to this method, we have to make sure that the inherited `init` and `initWithName:` methods also work for instances of C. This can be done just by covering B's `initWithName:` with a version that invokes `initWithName:fromFile:`.

> ```text
> - initWithName:(char *)string
> {
>     return [self initWithName:string fromFile:NULL];
> }
> ```

For an instance of the C class, the inherited `init` method will invoke this new version of `initWithName:` which will invoke `initWithName:fromFile:`. The relationship between these methods is diagrammed below.

**Figure 5-3 Covering the Designated Initializer**![\[image: ../Art/InitB\_C.gif\]](http://www.foldr.org/~michaelw/objective-c/ObjectiveC/Art/InitB_C.gif)

This figure omits an important detail. The `initWithName:fromFile:` method, being the designated initializer for the C class, will send a message to `super` to invoke an inherited initialization method. But which of B's methods should it invoke, `init` or `initWithName:`? It can't invoke `init`, for two reasons:

* Circularity would result \(`init` invokes C's `initWithName:`, which invokes `initWithName:fromFile:`, which invokes `init` again\).
* It won't be able to take advantage of the initialization code in B's version of `initWithName:`.

Therefore, `initWithName:fromFile:` must invoke `initWithName:`.

> ```text
> - initWithName:(char *)string fromFile:(char *)pathname
> {
>     if ( self = [super initWithName:string] )
>         . . .
> }
> ```

The general principle is this:  


| The designated initializer in one class must, through a message to `super`, invoke the designated initializer in an inherited class. |
| :--- |


Designated initializers are chained to each other through messages to `super`, while other initialization methods are chained to designated initializers through messages to `self`.

The figure below shows how all the initialization methods in classes A, B, and C are linked. Messages to `self` are shown on the left and messages to `super` are shown on the right.

**Figure 5-4 Initialization Chain**![\[image: ../Art/InitAll.gif\]](http://www.foldr.org/~michaelw/objective-c/ObjectiveC/Art/InitAll.gif)

Note that B's version of `init` sends a message to `self` to invoke the `initWithName:` method. Therefore, when the receiver is an instance of the B class, it will invoke B's version of `initWithName:`, and when the receiver is an instance of the C class, it will invoke C's version.  


#### Combining Allocation and Initialization

In Cocoa, some classes define creation methods that combine the two steps of allocating and initializing to return new, initialized instances of the class. These methods typically take the form `+` _`className...`_ where className is the name of the class. For instance, NSString has the following methods \(among others\):

> ```text
> + (NSString *)stringWithCString:(const char *)bytes;
> + (NSString *)stringWithFormat:(NSString *)format, ...;
> ```

Similarly, NSArray defines the following class methods that combine allocation and initialization:

> ```text
> + (id)array;
> + (id)arrayWithObject:(id)anObject;
> + (id)arrayWithObjects:(id)firstObj, ...;
> ```

Instances created with any of these methods will be deallocated automatically, so you don't have to release them unless you first retain them. Usually there are equivalent `-init...` methods provided along with these conveniences

Methods that combine allocation and initialization are particularly valuable if the allocation must somehow be informed by the initialization. For example, if the data for the initialization is taken from a file, and the file might contain enough data to initialize more than one object, it would be impossible to know how many objects to allocate until the file is opened. In this case, you might implement a `listFromFile:` method that takes the name of the file as an argument. It would open the file, see how many objects to allocate, and create a List object large enough to hold all the new objects. It would then allocate and initialize the objects from data in the file, put them in the List, and finally return the List.

It also makes sense to combine allocation and initialization in a single method if you want to avoid the step of blindly allocating memory for a new object that you might not use. As mentioned under ["The Returned Object" ](http://www.foldr.org/~michaelw/objective-c/ObjectiveC/5RunTime/Allocation__tialization.html#CJBBEEFH), an `init...` method might sometimes substitute another object for the receiver. For example, when `initWithName:` is passed a name that's already taken, it might free the receiver and in its place return the object that was previously assigned the name. This means, of course, that an object is allocated and freed immediately without ever being used.

If the code that checks whether the receiver should be initialized is placed inside the method that does the allocation instead of inside `init...`, you can avoid the step of allocating a new instance when one isn't needed.

In the following example, the `soloist` method ensures that there's no more than one instance of the Soloist class. It allocates and initializes an instance only once:

> ```text
> + soloist
> {
>     static Soloist *instance = nil;
>
>     if ( instance == nil )
>         instance = [[self alloc] init];
>     return instance;
> }
> ```

#### Deallocation

The NSObject class defines a `dealloc` method that relinquishes the memory that was originally allocated for an object. You rarely invoke `dealloc` directly, however, because Cocoa provides a mechanism for the automatic disposal of objects \(which makes use of `dealloc`\). For more information on this automatic object disposal mechanism, see the introduction to the Foundation Framework reference.

The purpose of a `dealloc` message is to deallocate all the memory occupied by the receiver. NSObject's version of the method deallocates the receiver's instance variables, but doesn't follow any variable that points to other memory. If the receiver allocated any additional memory-to store a character string or an array of structures, for example-that memory must also be deallocated \(unless it's shared by other objects\). Similarly, if the receiver is served by another object that would be rendered useless in its absence, that object must also be deallocated.Therefore, it's necessary for subclasses to override NSObject's version of `dealloc` and implement a version that deallocates all of the other memory the object occupies. Every class that has its objects allocate additional memory must have its own `dealloc` method. Each version of `dealloc` ends with a message to `super` to perform an inherited version of the method, as illustrated in the following example:

> ```text
> - dealloc {
>     [companion release];
>     free(privateMemory);
>     vm_deallocate(task_self(), sharedMemory, memorySize);
>     [super dealloc];
> }
> ```

By working its way up the inheritance hierarchy, every `dealloc` message eventually invokes NSObject's version of the of the method.  


