## 全量埋点工程化及fishhook延展



### 项目当前状况

目前翼课网项目中采用的是代码手动埋点方式，此方案有如下弊端：

1）会对业务代码造成一定的**污染**，违背了**功能单一原则**

2）后续**埋点成本较高**，需考验程序员的耐心和细心，相应的事件嵌入有出现错位或遗漏的风险

3）替换不便，如从友盟迁移至诸葛IO

场景1:以登陆页面为例，我们要埋如下的点： （1）登录点击 （2）注册点击（3）找回密码点击 （4）学生登录点击 （5） 返回点击 等等，以上这些行为非用户的核心交互行为，但是为了还原用户操作闭环，我们不得不埋，增加了部分工作量且此部分均为重复代码。

场景2:如果我们想了解用户在某一些页面的停留时间，那么我们需要在所需页面的生命周期起止进行埋点，每一个页面分别计算进入和离开的差值时间，同场景一，这样做需要大量的人工投入。

场景3:以视频课程为例，我们能够知道用户点击了课程列表中的某一个课程，但如果想知道究竟哪一个课程的点击量比较大，可能就需要对事件携带的属性进行漏斗分析获取。

以上种种问题或多或少与我们的代码手动埋点的特性有关，手动代码工作量大，事件映射关联强。



### “无痕“全量埋点

所谓“无痕”指的是我们不在业务代码中进行埋点操作，而将埋点集中于控制器的生命周期，按钮的点击事件，各种控件的点击回调方法中，无差别，无区分，全量上传。

我们上传的**事件名**是**按钮的名称**、**控制器的映射中文字段**、**控件的核心数据属性**

例如： 登录按钮在app中显示的名称是“登录”，那么点击登录按钮时，我们上传的事件名就是 event = “登录”。

用户答题控制器在本地保存的映射表中，相对应的映射中文名“用户正式答题”，上报的是 event = “用户正式答题” ，usingTime = “50s”。

用户点击了课程列表中的某个课程，我们上传的是 这个课程的名称 event = "课程进入 中考英语（广东省卷）高分揭秘体验课程" 。

<img src="https://github.com/wondernick/ARTS/blob/master/image/IMG_2243.PNG" style="zoom:30%;" />



说完优点，聊聊缺点：

1）全面了，无死角了，量也就上来了，产品和运营去做归纳分析时的成本也就上去了。

2）之所以全量要加一个**“”** 是因为这种全量方式也是有**局限性**的，一些特殊控件如**UISwitch**（护眼模式的开关），**网络请求的回调**，是无法通过通过这种方式进行埋点的，这些还需要配合一些手动埋点处理。

通过以上的描述，我们可以了解到，通过 hook 技术，我们可以很简单的获取到所有的案件的点击行为,所有页面的浏览时长，所有列表的栏目选择，当然这样也会牺牲一些特性化的属性，如果非要获取一些特性化的属性，也是可以进行相应定制的。

说完了它对业务的影响，我们接下来看看技术落地。

分别添加 NSObject，UIButton，UICollectionView，UITableView的分类，在分类中hook相应的生命周期。

```markdown
//
//  NSObject+NSObject_MethodSwizzling.m
//  EKWRace
//
//  Created by zhangyang on 2020/5/21.
//  Copyright © 2020 ekwing. All rights reserved.
//

#import "NSObject+NSObject_MethodSwizzling.h"
#import <objc/runtime.h>

@implementation NSObject (NSObject_MethodSwizzling)

#pragma mark - public Method

+ (void)zy_swizzleMethod:(SEL)originalSelector swizzledSelector:(SEL)swizzledSelector
{
    class_swizzleInstanceMethod(self, originalSelector, swizzledSelector);
}

+ (void)zy_swizzleClassMethod:(SEL)originalSelector swizzledSelector:(SEL)swizzledSelector
{
    
    Class class2 = object_getClass(self);
    class_swizzleInstanceMethod(class2, originalSelector, swizzledSelector);
}

void class_swizzleInstanceMethod(Class class, SEL originalSEL, SEL replacementSEL)
{
    Method originMethod = class_getInstanceMethod(class, originalSEL);
    Method replaceMethod = class_getInstanceMethod(class, replacementSEL);
    
    if(class_addMethod(class, originalSEL, method_getImplementation(replaceMethod),method_getTypeEncoding(replaceMethod)))
    {
        class_replaceMethod(class,replacementSEL, method_getImplementation(originMethod), method_getTypeEncoding(originMethod));
    }else {
        method_exchangeImplementations(originMethod, replaceMethod);
    }
}

@end
```

在NSObject分类中构建 hook 基础方法。

其他分类的相关就不大段的粘贴了，直接带大家去工程中看。

这个技术的核心是利用OC的runtime机制，把各个基类的系统方法置换为我们自己的实现，这种实现方式也叫做AOP（面向切面编程）

说完利用runtime进行的全量埋点，我门再来探究一下与之很相似的 hook 技术：**fishhook**



### Fishhook

Fishhook 是 facebook开源的一个对Mach-O文件进行动态符号重绑定工具库。它与本文上半部分提到的hook技术都是利用语言的动态特性就行重绑定，但是fishhook 操作的是外部C函数，而class_replaceMethod操作的是运行时中内部函数。

MachO 文件： 代码段（可读 可执行 不可写） 数据段（可读可写）。

DYLD - 动态加载 存在于IOS系统及mac系统，UIKit，Foundation均是由它加载的。 

为了应用能够调用系统的框架函数

早期的解决方法： 把默认内存地址进行代码重定向到系统函数的调用

PIC技术（位置代码独立）：

1）在 Mach-O 的 _DATA 段生成一块区域，这个区域就是 **符号表**！

2）DYLD将系统函数的真实地址赋值到数据段中去 ，这就是 **符号的绑定** ，再回头回味一下 Fishhook中执行hook的函数名称  “**rebind_symbols**” ！

<img src=""https://github.com/wondernick/ARTS/blob/master/image/fishhook%E5%AE%9E%E7%8E%B0%E5%BA%95%E5%B1%82%E5%8E%9F%E7%90%86.png" style="zoom:30%;" />



​                                                                       Fishhook底层实现原理



FIshhook的应用： 可以hook系统的C函数 外部函数  比如抖音的启动优化，对OC方法进行二进制重排，这里需要hook所有的OC方法，借助Fishhook就可以hook 到 **objc_msgSend**，从而做到hook所有的OC方法！ 



总结：通过运行时 Method Swizzle 我们可以hook到各个基类的OC方法，通过Fishhook hook住各个系统C函数，从而达成对整个项目的面向切面编程。







