#Swift并发编程基础知识
[URL](https://medium.com/flawless-app-stories/basics-of-parallel-programming-with-swift-93fee8425287)
[Operations](https://medium.com/flawless-app-stories/parallel-programming-with-swift-operations-54cbefaf3cb0)

##Synchronous vs Asynchronous(同步vs移步)
同步和异步的具体区别在哪里, 想象一下我们有一系列的任务需要去处理. 当我们同步的去处理这些任务的时候, 我们会在第一个任务处理完成的时候才去处理下一个任务. **(FIFO Queue)**

###Synchronous(同步)

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fru9hnxerej30ld04yt8t.jpg)
使用代码来表示就是: **方法的每个语句都将按顺序执行**

```c
func method1() {
	statement1()
	statement2()
	statement3()
}
```
*总结就是: 所以基本上同步意味着一次处理一个任务*
###异步
相比之下，异步处理同时处理多个任务。它将例如处理任务item1，暂停item2，然后继续并完成item1。
我们可以看到**statement2**会在callBack回调执行之前先被执行/
```
func method2() {
	statement1 {
		callback()
	}
	statement2()
}
```

##Concurrency vs Parallelism(并发vs并行)



