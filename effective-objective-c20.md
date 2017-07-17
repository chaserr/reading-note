#Effective Objective -C2.0

1. OC语言中对象所占内存总是分配在‘堆空间（heap space）’

##2. 在类的头文件中尽量少引入其他头文件
1. 除非有必要，否则不要引入头文件，一般来说，应在某个类的头文件中使用向前声明`@class someClass;`来提及别的类，并在实现文件中引入那些来的头文件，这样做可以尽量降低类之间的耦合
2. 有时候无法使用向前声明，比如要声明某个类遵循一项协议，这种情况下，尽量吧“该类遵循某协议”的这条声明移到"分类"中，如果 不行，酒吧协议单独放在一个头文件中，然后将其引入。
##3. 多用字面量语法，少用与之等价的方法
##4. 多用类型常量， 少用#define 预处理命令
i.e:
```objectivec
static const NSTimeInterval kAnimationDuration = 0.3;
```
>延伸：
> 1. static 修饰符意味着该变量仅在定义此变量的编译单元中可见，oc 中也就是指类的实现文件中（.m文件）。
> 2. const 修饰符意味着该变量不可变
> 3. 若希望外部文件可以访问，可以在.h文件中用 `extern`关键字声明.而在.m文件中声明全局常量，这时候不需要 `static`关键字修饰

>要点：
1. 在实现文件中使用 statci const 来定义“只在便一单元内可见的常量”，由于此常量不在全局符号表中，所以无需为其名称加前缀。
2. 在头文件中使用‘extern’关键字来声明全局常量，并在相关实现文件中定义其值，这种长量要出现在全局符号表中，所以其名称应加以区隔，通常用与之相关的类名做前缀。

##5. 用枚举表示状态，选项，状态码
>要点：
1. 用`NS_ENUM`和`NS_OPTIONS`宏来定义枚举类型，并指明其底层数据类型，这样做可以确保每具使用开发者所选的底层数据类型来实现出来的，而不是编译器所选的类型。

##6. 理解属性
1. 通过`@synthesize`关键字可以指定实例变量的名称：
```c
@implementation
@synthesize firstName = _myFirstName;
@end
```
2. 如果不想编译器自动合成存取方法（set,get）则可以自己实现，如果你只实现了其中一个存取方法，那么另一个还是会由编译器来合成
3. 或者使用`@dynamic`关键字来告诉编译器，不要自动创建实现属性所有的实例变量，也不要为其创建存取方法。

##7. 在对象内部尽量直接访问实例变量
> _firstName直接访问： self.firstName属性访问--两者区别
> 1. 直接访问不经过 OC 的方法派发步骤，
> 2. 直接访问不会调用其“设置方法”
> 3. 直接访问不会触发“KVO-键值观测”

综上要点：
1. 在对象内部读取数据时，应该直接通过实例变量来读，而写入数据时，则应通过属性来写。
2. 在初始化方法以及 dealloc方法中，勇士应该直接通过实例变量来读写数据
3. 有时候会使用懒加载初始化技术，这时候应该通过属性来读取数据。

##8. 理解“对象等同性”这一概念
判断两个NSObject对象是否相等要覆写`isEqual`和`hash`方法：
```c
- (BOOL)isEqual:(id)object{

    if (self == object) return YES;
    if ([self class] != [object class]) return NO;
    // 判断每个属性是否相等
    SomeClass *class = (SomeClass *)object;
    if (![class.property1 isEqualToString: object.property1]) {
        return NO;
    }else if (![class.property2 isEqualToString: object.property2]){
    
        return NO;
    }else if (/*...*/){
//        ...
    }
    return YES;
}

- (NSUInteger)hash{

    NSUInteger property1Hash = [property1 hash];
    NSUInteger property2Hash = [property2 hash];
//    ...
    return property1Hash^property2Hash^//...
}
```

要点：
1. 若要检测对象的等同性，要提供`isEqual`和`hash`方法。
2. 相同的对象必须具有相同的哈希码，但是两个哈希码相同的对象却未必相同。
3. 不要盲目的逐个检测每条属性。
4. 编写 hash方法的时候，应该使用计算速度快而且哈希吗碰撞几率低的算法。

##9. 以‘类族模式’隐藏实现细节
##10. 在即有类中使用关联对象存放自定义数据
|    关联类型   |等效的@property属性|
|:-----------------------:|:----:|
|`OBJC_ASSOCIATION_ASSIGN`|assign|
|`OBJC_ASSOCIATION_RETAIN_NONATOMIC`|assign|
|`OBJC_ASSOCIATION_COPY_NONATOMIC`|assign|
|`OBJC_ASSOCIATION_RETAIN`|assign|
|`OBJC_ASSOCIATION_COPY`|assign|


使用i.e.:
1. 使用运行时给分类添加属性
2. 想在一个类中处理多个 alertview属性。可以给某个alertview绑定一个与之关联的‘block’。
```c
#import<objc/runtime>
static void *AssociationKey = @"AssocianKey";

- (void)showAlert
{
    void (^block)(NSInteger) = ^(NSInteger buttonIndex){
    
        if (buttonIndex == 0) {
            [self 取消];
        }else{
        
            [self 继续];
        }
    };
    
    objc_setAssociatedObject(alert, AssociationKey, block, OBJC_ASSOCIATION_COPY);
}
- (void)取消{}
- (void)继续{}

- alertView(UIAlertView*)alertView clickedButtonAtIndex:(NSInteger)buttonIndex{

    void (^block)(NSInteger) = objc_getAssociatedObject(alertView, AssociationKey);
    block(buttonIndex);
}
```

要点：
1. 通过关联对象机制把两个对象连起来
2. 定义关联对象时壳置顶内存管理语义，用以模仿定义属性时所采用的‘拥有关系’和‘非用有关系’
3. 只有在其他做法不可行时才应选用关联对象，因为这种做法通常会难以查找 bug

 ##11. 理解 objc_msgSend的作用
 ##12. 理解消息转发机制
 > 见 iOS知识积累1.6章 `runtime`

在对象无法解读消息后，首先将调用其所属类的下列方法`+(BOOL)resolveInstanceMethod:(SEL)selector`其返回值为 Boolean 类型，表示这个类能否新增一个实例方法用以处理此 selector，加入尚未实现的方式不是实例方法二十类方法，那么会调用另外一个方法`resolveClassMethod`。
使用这种方法的前提是，相关方法的实现代码已经写好，只等着运行的时候动态插在类里面就可以，此方案常用来实现@dynamic属性。
```c
+ (BOOL)resolveInstanceMethod:(SEL)selector{
    NSString *selectorString = NSStringFromSelector(selector);
    if(/*selector is from a @dynamic property*/){
        if([selectorString hasPrfix:@"set"]){
            class_method(self,selector,(IMP)autoDictionarySetter,@"v@:@");
        }else{
            class_method(self,selector,(IMP)autoDictionaryGetter,@"@@:");
        }
        return YES;
    }
    return [super resolveInstanceMethod:selector];
}

id autoDictionaryGetter(id self,SEL _cmd){
    
}

void autoDictionarySetter(id self,SEL _cmd, id value){
    
}
```
首先将选择子化为字符串，然后检测其是否表示设置方法，若前缀为 set, 则表示设置方法，否则即使获取方法，不管哪种情况，都会把处理理该选择子的方法加到当前类里面，所添加的方法是用纯 C 函数实现。

消息转发流程

![](/assets/B8C0FB39-6DCC-4ABF-8F39-5DEAD11D9A0F.png)

要点：
1. 若对象无法响应某个选择子，则进入消息转发流程
2. 通过运行期的动态方法解析功能，可以在需要用到某个方法时再将其加入类中。
3. 对象可以把其无法解读的某些选择子转交给其他的对象来处理
4. 经过上述两步之后，如果还没有办法处理选择子，那就启动完整的消息转发机制。

##13. 用‘方法调配技术(metho swizzling)’调试‘黑盒方法’
##14. 理解‘类对象’的用意
##15. 用前缀避免命名空间冲突 
##16. 提供‘全能初始化方法’（便利构造器）
##17. 是写 description 方法
##18. 尽量使用不可变对象
##19. 使用清晰而协调的命名方式
##20. 为私有方法名加前缀
##21. 理解objective-c错误模型
##22. 理解 NSCopying 协议
1. 浅拷贝：指针拷贝，指向统一内存地址；深拷贝：值拷贝，指向不同内存地址，需要为拷贝的值再分配一份内存来存放
##23. 通过委托与数据源协议进行对象间通信
1、 若有必要，可实现含有位段的结构体，将委托对象是否能响应相关协议方法这一信息缓存在其中
##24. 将类的实现代码分散到便于管理的数个分类之中。
##25. 总是为第三方类的分类名称添加前缀
##26. 不要在分类中声明属性
##27. 使用分类隐藏实现细节
##28. 听过协议提供匿名对象
##29. 理解引用计数
##30. 以 ARC 简化引用计数
##31. 在dealloc 方法中只释放引用并解除监听
##32. 编写‘异常安全代码’时留意内存管理问题
##33. 以弱引用避免保留环
##34. 以‘自动释放池块’降低内存峰值
1. 一般用在循环引用内部
i.e.：
```c
for (int i=0;i<10000;i++){
    @autoreleasepool {
       [self doSomething];
    }
}
```
##35. 用僵尸对象调试内存管理问题
##36. 不要使用 retainCount
##37. 理解‘block’ 这一概念
1. block 类似于y
