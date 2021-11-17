### 一、category的定义
Category在iOS开发中使用非常的频繁,特别是在为系统类进行拓展的时候，我们可以不用继承系统类，直接给系统类添加方法，最大程度的体现了Objective-C的动态语言特性。（注：swift没有category）

```
@interface NSObject(Category)

@end
```

### 二、category的原理
- 实现原理是将category中的方法，属性，协议数据放在category_t结构体中，然后将结构体内的方法列表拷贝到类对象的方法列表中。 Category可以添加属性，但是并不会自动生成成员变量及set/get方法。因为category_t结构体中并不存在成员变量。通过之前对对象的分析我们知道成员变量是存放在实例对象中的，并且编译的那一刻就已经决定好了。而分类是在运行时才去加载的。那么我们就无法再程序运行时将分类的成员变量中添加到实例对象的结构体中。因此分类中不可以添加成员变量。
- 在往深一点的回答就是 类在内存中的位置是编译时期决定的， 之后再修改代码也不会改变内存中的位置，class_ro_t 的属性在运行期间就不能再改变了， 再添加方法是会修改class_rw_t 的methods 而不是class_ro_t 中的 baseMethods。

### 三、category的特点
1. 定义的方法不一定要在 @implement 中实现，也可以在其它位置实现，但是当调用的时候，依据继承树没有找到该方法的实现就会crash。
2. category的方法优先级最高，可以覆盖原类的方法。
3. category的方法中，不可以调用super方法。
4. 在实际开发中要注意的是,Category 方法可能会覆盖于同一个类class 的其它 category 中的方法。但也可能被覆盖，因为不法预知他们的加载优先顺序，出现这种情况通常会在编译时出错。通常通过加前缀来避免问题。
5. Category理论上不能添加成员变量,但是可以使用@dynamic(即运行时Runtime)来弥补这种不足，[即手动实现getter和setter](https://cloud.tencent.com/developer/article/1391529)。（原理中解释）


### 四、extension的定义
- extension被开发者称之为扩展、延展、匿名分类。extension看起来很像一个匿名的category，但是extension和category几乎完全是两个东西。和category不同的是extension不但可以声明方法，还可以声明属性、成员变量。extension一般用于声明私有方法，私有属性，私有成员变量。
- category是拥有.h文件和.m文件的东西。但是extension不然。extension只存在于一个.h文件中，或者extension只能寄生于一个类的.m文件中。


### 五、两者区别
- extension可以添加实例变量，而category是无法添加实例变量的（因为在运行期，对象的内存布局已经确定，如果添加实例变量就会破坏类的内部布局，这对编译型语言来说是灾难性的）
- extension在编译期决议，它就是类的一部分，但是category则完全不一样，它是在运行期决议的。extension在编译期和头文件里的@interface以及实现文件里的@implement一起形成一个完整的类，它、extension伴随类的产生而产生，亦随之一起消亡。
- extension一般用来隐藏类的私有信息，你必须有一个类的源码才能为一个类添加extension，所以你无法为系统的类比如NSString添加extension，除非创建子类再添加extension。而category不需要有类的源码，我们可以给系统提供的类添加category。
- extension可以添加实例变量，而category不可以。
- extension和category都可以添加属性，但是category的属性不能生成成员变量和getter、setter方法的实现。


### 六、引伸
- 关联对象并不是存储在关联对象本身内存中，而是存储在全局统一的一个容器中；
- 关联对象并不是存储在关联对象本身内存中，而是存储在全局统一的一个容器中；
- 使用 AssociationsManagerLock 自旋锁保证了线程安全
- 被关联的对象的生命周期内要比对象本身释放晚很多， 它们会在被 NSObject -dealloc 调用的 object_dispose() 方法中释放。

### Reference
1. https://juejin.cn/post/6854573212165111822
2. [应用场景](http://www.cocoachina.com/articles)
