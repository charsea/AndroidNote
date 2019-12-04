### ThreadLocal

`ThreadLocal` 是多线程都需要使用一个变量，但是这个变量的值不需要各个线程间共享，每个线程都有自己的这个变量的值
ThreadLocal 负责管理 ThreadLocalMap ，包括插入，删除 等等.另一方面来说 ThreadLocal 基本上就相当于 门面设计模式中的一个Facade类。key就是 ThreadLocal 对象自己，同时，很重要的一点:就ThreadLocal把 Map 存储在当前线程对象里面
ThreadLocal 实例本身是不存储值，它只是提供了一个在当前线程中找到副本值的key(它自己就是ThreadLocalMap的key)
2.是ThreadLocal包含在Thread中，而不是Thread包含在 ThreadLocal 中，有些小伙伴会弄错他们的关系。
get()操作获取ThreadLocal中对应的当前线程存储的值的时候,先得到当前线程的 Thread 对象,进而获取此线程对象中维护的 ThreadLocalMap ,然后判断ThreadLocalMap是否存在,如果存在,以当前的 ThreadLocal 为 key ,调用 ThreadLocalMap 的 getEntry() 方法获取对应的存储实体 e ,找到对应的value值,也就是我们想要的此线程对应的ThreadLocal值,然后返回。

问题1:`ThreadLocalMap` 中存储实体`Entry`使用当前 `threadLocal` 实例作为key,但是这个`Entry`继承了弱引用`WeakReference`,什么是弱引用?为什么要这么设计?这样会带来什么问题？

官方给的说法是: 为了应对非常大和长时间的用途，哈希表使用弱引用的 key。
 我们假设我们自己设计的时候key 使用的强引用和弱引用

1.  `key`使用强引用:如果引用`ThreadLocal`的对象`ThreadLocalRef`被回收了,但是`ThreadLocalMap`还持有`ThreadLocal`对象的强引用,如果没有手动删除的话`ThreadLocal`不会被回收,这样会导致`Entry`内存泄露
2.  `key`使用弱引用:引用的`ThreadLocal`的对象`ThreadLocalRef`被回收了，由于`ThreadLocalMap`持`有ThreadLocal`的弱引用，即使没有手动删除，`ThreadLocal`也会被回收。`value`在下一次`ThreadLocalMap`调用`get(),set(),remove()`的时候会被清除。
    比较上面的2种情况,我们会发现:`ThreadLocalMap`的生命周期和`Thread`一样长,如果都没有手动删除`key`都会导致内存泄露。但是弱引用多了一层保障,就是`value`在下一次`ThreadLocalMap` 调用 `get(),set(),remove()` 的时候会被清除。
    因此可知,`ThreadLocal`发生内存泄露的根源是由于`ThreadLocal`的生命周期和`Thread`一样长,在没有手动删除对应的`key`的时候就会导致内存泄露,并不是因为弱引用导致的,弱引用只是优化的方式。
    综上分析：为了避免内存的泄露,每次使用完 `ThreadLocal` 的时候都需要调用 `remove()` 方法来擦除数据。并且大规模网站一般都会使用到线程池,如果没有及时清理的话不仅是内存泄露,业务逻辑可能也会被影响。所以养成好习惯,记得擦除数据。