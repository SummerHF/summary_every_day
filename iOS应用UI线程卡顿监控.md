#iOS应用UI线程卡顿监控
**iOS**设备虽然在硬件和软件层面一直在优化, 但还是有不少坑导致**UI**线程的卡顿. 对于程序员来说, 除了增加自身知识储备和养成良好的编程习惯之外, 如果能有一套机制自动预报**卡顿**并检测出导致该**卡顿**的代码位置自然更好.本文就可能的实现方案做一些探讨和分析.

##解决方案分析
简单来说, 主线程为了达到接近**60fps**的绘制效率, 不能在UI线程有单个超过（1/60s =16ms）的计算任务.通过**instrument**设置**16ms**的采样率可以检测出绝大部分这种耗时的任务, 但有以下缺点:

* **instrument profile**一次重新编译, 时间较长.
* 只能针对特定的操作场景进行检测， 要预先知道卡顿产生的场景.
* 每次猜测，更改，再猜测再以此循环, 需要重新**profile**.

我的目标方案是, 检测能够自动发生, 并不需要开发人员做任何预先配置或**profile**.运行时发现卡顿即时通知开发人员导致卡顿的函数调用栈.

###方案一: 基于Runloop

主线程绝大部分计算或者绘制任务都是以**Runloop**为单位发生. 单次**Runloop**如果时长超过16ms, 就会导致UI体验的卡顿. 那如何检测单次**Runloop**的耗时咧?

**Runloop**的生命周期及运行时机制虽然不透明， 但苹果提供了一些API去检测部分行为.我们可以通过如下代码监听**Runloop**每次进入的事件:

```
#pragma mark - : Extension

@interface AppDelegate (Extension)

@end

@implementation AppDelegate(Extension)

static void RunloopObserverCallBack(CFRunLoopObserverRef observer, CFRunLoopActivity activity, void *info) {
    switch (activity) {
        case kCFRunLoopEntry: {
            NSLog(@"enter runloop");
        }
            break;
        case kCFRunLoopExit: {
            NSLog(@"leave runloop");
        }
            break;
        case kCFRunLoopBeforeTimers: {
            NSLog(@"before timers");
        }
            break;
        case kCFRunLoopAfterWaiting: {
            NSLog(@"after waiting");
        }
            break;
        default:
            break;
    }
}

+ (void)load {

    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        CFRunLoopRef runloop = CFRunLoopGetCurrent();
        /**Observer of runloop*/
        CFRunLoopObserverRef enterObserver;
        enterObserver = CFRunLoopObserverCreate(CFAllocatorGetDefault(), kCFRunLoopEntry | kCFRunLoopExit, YES, -0x7FFFFFFF, RunloopObserverCallBack, NULL);
        CFRunLoopAddObserver(runloop, enterObserver, kCFRunLoopCommonModes);
        CFRelease(enterObserver);
    });
}

@end
```
看起来**kCFRunLoopExit**减去**kCFRunLoopEntry**的时间, 即为一次**Runloop**所花费的时间. 这个方案的缺陷在于无法定位到具体的函数, 所以不符合我们的目标方案

###方案二:基于线程

最理想的方案是让**UI**线程**主动汇报**当前耗时的任务, 听起来简单做起来不轻松.
我们可以假设这样一套机制, 每隔**16**ms让UI线程来报道一次, 如果**16ms**之后UI线程没来报道, 那就一定是在执行某个耗时的任务, 这种抽象的描述翻译成代码, 可以用如下表述:

```
我们启动一个worker线程, worker线程每隔一小段时间(delta)ping一下主线程(发送一个Notification), 如果主线程此时有空, 必然能接受到这个通知, 并ping一下(发送另一个Notification),如果worker线程超过delta时间没有ping的回复,那么可以推测UI线程必然在处理其他任务了
```

