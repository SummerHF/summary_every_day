#GCD
## Dispatch Group
在追加到**Dispatch Queue**中的多个处理全部结束后要执行结束处理, 这种情况经常出现. 只用一个**Serial Dispatch Queue**时, 只要将想执行的处理全部追加到该**Serial Dispatch Queue**中并在最后追加结束处理即可，而对于**Concurrent Dispatch Queue**而言或同时使用多个**Dispatch Queue**时, 因为其执行顺序不是固定的, 源代码就会变得颇为复杂.

这种情况下使用**Dispatch Group**

```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    dispatch_group_t group = dispatch_group_create();
    dispatch_group_async(group, queue, ^{
        NSLog(@"block0");
    });
    dispatch_group_async(group, queue, ^{
        NSLog(@"block1");
    });
    dispatch_group_async(group, queue, ^{
        NSLog(@"block2");
    });
    dispatch_group_notify(group, queue, ^{
        NSLog(@"all finished");
    });
    
    //// 输出为
   	2018-07-17 14:02:25.771090+0800 Test[13335:517831] block2
		2018-07-17 14:02:25.771090+0800 Test[13335:517841] block1
		2018-07-17 14:02:25.771090+0800 Test[13335:517832] block0
		2018-07-17 14:02:25.771231+0800 Test[13335:517841] all finished
```

##dispatch_sync同步
**dispatch_sync**函数, 意味着同步, 也就是将指定的**block**同步追加到指定的**Dispatch Queue**中. 在追加**Block**结束之前, **dispatch_sync**函数会一直等待.

##dispatch_apply
该函数将指定的**Block**追加到指定的**Dispatch Queue**中, 并且等待全部处理执行结束.

##dispatch semaphore
当并行执行的处理更新数据时, 会产生数据不一致的情况, 有时应用程序还会异常结束. 虽然使用**Serial Dispatch Queue**和**dispatch_queue_async**函数可避免这类问题, 但有必要进行更细粒度的排他控制.

```
 dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    /// 初始值为1, 保证可访问NSMutableArray类对象的线程同时只能有一个.
    dispatch_semaphore_t semaphore = dispatch_semaphore_create(1);
    NSMutableArray *array = [[NSMutableArray alloc] init];
    for (int i = 0 ; i < 1000; i++) {
        dispatch_async(queue, ^{
            /// 由于Dispatch Semaphore的计数值达到大于等于1， 所以计数值减1
            dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
            [array addObject:[NSNumber numberWithInt:i]];
            dispatch_semaphore_signal(semaphore);
        });
    }
```

##Dispatch I/O
**Dispatch Data** && **Dispatch I/O**

