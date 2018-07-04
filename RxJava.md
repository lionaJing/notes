## Backpressure
当被观察者(Observable)的生成速度大于观察者(Observer)的处理速度会出现 Backpressure 现象(只有在异步环境下会发生).
(Backpressure 指的是在 Buffer 有上限的系统中，Buffer 溢出的现象；它的应对措施只有一个：丢弃新事件。by 扔物线)
会抛出 MissingBackpressureException 异常信息,

## Hot and Cold Observables
* Cold Observables：当被订阅后才开始发送事件的Observable(每个Subscriber都能接收到完整的事件)
* Hot Observables:  不管有没有被订阅,都会去发送事件的 Observable

Hot Observable 是不支持背压的,Cold Observable 有一部分并不支持背压(比如interval，timer等操作符创建的Observable)
rxJava2.0 已经解决了这个问题(Observable,有的支持背压,有的不支持)

cold observable 相当于响应式拉(就是observer处理完了一个事件就从observable拉取下一个事件),hot observable通常不能很好的处理
响应式拉模型,但它却是处理流量控制问题的不二候选人,例如使用onBackpressureBuffer或者onBackpressureDrop 操作符,和其他操作符比
如operators, throttling, buffers, or windows.

observeOn这个操作符内部有一个缓冲区，Android环境下长度是16.
我们经常用到的Observable.create 就是 Cold Observable,而 just, range, timer 和 from 这些创建的同样是 Cold Observable.
我们一般使用的都是Cold Observable,除非特殊需求,才会使用Hot Observable

## 背压操作符
rxJava1.X 针对不支持背压提供的操作符有：sample(),throttleLast(),throttleFirst(),throttleWithTimeout(),
debounce(), onBackpressurebuffer(),onBackpressureDrop()

## RxJava1.x vs RxJava2.x
* 2.x 发送事件时不能传 null,否则会抛出空指针异常
* 2.x 中 Observable 不再支持背压, 用 Flowable 来支持背压
* 线程调度方面 2.x 取消 Schedulers.immediate()

[RxJava2 教程1](https://www.jianshu.com/p/a93c79e9f689) [RxJava2 教程2](https://www.jianshu.com/p/d149043d103a)

## RxJava2 Backpressure
Observable拆分成了新的 Observable 和 Flowable,Observable 不具备处理背压能力,Flowable 为 解决背压问题而生
Backpressure 只在异步情况下产生,RxJava2 的处理策略仅仅是对观察者接收事件的处理

1.ERROR
产生背压问题会抛出 MissingBackpressureException 异常信息
在异步调用时,RxJava中有个缓存池，用来缓存消费者处理不了暂时缓存下来的数据,缓存池的默认大小为128,即只能
缓存128个事件.无论request()中传入的数字比128大或小,缓存池中在刚开始都会存入128个事件
```
Flowable.create(new FlowableOnSubscribe<>(),BackpressureStrategy.ERROR)
	.subscribe(new Subscriber());
```

2. BUFFER
把RxJava中默认的只能存128个事件的缓存池换成一个大的缓存池，支持存很多很多的数据,比较消耗内存

3. DROP  LATEST
DROP 丢弃事件,LATEST使消费者能够接收到生产者产生的最后一个事件


