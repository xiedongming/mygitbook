# NSThread

  
 -  一个NSThread对象就代表一条线程

###创建、启动线程
```objc
NSThread *thread = [[NSThread alloc] initWithTarget:self selector:@selector(run) object:nil];
[thread start];
// 线程一启动，就会在线程thread中执行self的run方法

//主线程相关用法
+ (NSThread *)mainThread; // 获得主线程
- (BOOL)isMainThread; // 是否为主线程
+ (BOOL)isMainThread; // 是否为主线程
```
###其他用法
```objc
//获得当前线程
NSThread *current = [NSThread currentThread];

//线程的名字
- (void)setName:(NSString *)n;
- (NSString *)name;
```
###其他创建线程方式
```objc
//创建线程后自动启动线程
[NSThread detachNewThreadSelector:@selector(run) toTarget:self withObject:nil];

//隐式创建并启动线程
[self performSelectorInBackground:@selector(run) withObject:nil];
```
 - 上述2种创建线程方式的优缺点
 - 优点：简单快捷
 - 缺点：无法对线程进行更详细的设置


### 线程状态  
alloc 创建空间     star 创建可调度线程池 （变成就绪状态）<———>cpu 调度（运行）——sleep (阻塞)———又变成就绪zhuan状态———完成死掉

###控制线程状态
```objc
//启动线程
- (void)start; 
// 进入就绪状态 -> 运行状态。当线程任务执行完毕，自动进入死亡状态

//阻塞（暂停）线程
+ (void)sleepUntilDate:(NSDate *)date;
+ (void)sleepForTimeInterval:(NSTimeInterval)ti;
// 进入阻塞状态

//强制停止线程
+ (void)exit;
// 进入死亡状态
```
注意：一旦线程停止（死亡）了，就不能再次开启任务

###安全隐患解决 – 互斥锁

互斥锁使用格式
```objc
@synchronized(锁对象) { // 需要锁定的代码  }
```
注意：锁定1份代码只用1把锁，用多把锁是无效的

 
- 互斥锁的优缺点
  - 优点：能有效防止因多线程抢夺资源造成的数据安全问题

  - 缺点：需要消耗大量的CPU资源

- 互斥锁的使用前提：多条线程抢夺同一块资源

- 相关专业术语：线程同步
- 线程同步的意思是：多条线程在同一条线上执行（按顺序地执行任务）   互斥锁，就是使用了线程同步技术



### 原子和非原子属性



- OC在定义属性时有nonatomic和atomic两种选择
  - atomic：原子属性，为setter方法加锁（默认就是atomic）
  - nonatomic：非原子属性，不会为setter方法加锁

###原子属性和非原子属性的选择

- nonatomic和atomic对比
 - atomic：线程安全，需要消耗大量的资源
 - nonatomic：非线程安全，适合内存小的移动设备

- iOS开发的建议
 - 所有属性都声明为nonatomic
 - 尽量避免多线程抢夺同一块资源
 - 尽量将加锁、资源抢夺的业务逻辑交给服务器端处理，减小移动客户端的压力


###线程间通信

- 什么叫做线程间通信
 - 在1个进程中，线程往往不是孤立存在的，多个线程之间需要经常进行通信
- 线程间通信的体现
 - 1个线程传递数据给另1个线程
 - 在1个线程中执行完特定任务后，转到另1个线程继续执行任务

- 线程间通信常用方法

```objc
  - (void)performSelectorOnMainThread:(SEL)aSelector withObject:(id)arg waitUntilDone:(BOOL)wait;
  - (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(id)arg waitUntilDone:(BOOL)wait;
```

###检测数据耗时操作（消耗了多少秒）


```objc
1.  
CFTimeInterval begin =CFAbsoluteTimeGetCurrent();
   //中间加代码块
   CFTimeInterval end = CFAbsoluteTimeGetCurrent();
   NSLog(@"----%f",end - begin);
2. 
    //    NSDate * begin = [NSDate date];
    //    NSDate * end = [NSDate date];
    //    NSLog(@"----%f",[end timeIntervalSinceDate:begin]);
```
