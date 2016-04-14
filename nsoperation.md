# NSOperation
##简介

###NSOperation的作用
- 配合使用NSOperation和NSOperationQueue也能实现多线程编程
- NSOperation和NSOperationQueue实现多线程的具体步骤
 - 先将需要执行的操作封装到一个NSOperation对象中
 - 然后将NSOperation对象添加到NSOperationQueue中
   系统会自动将NSOperationQueue中的NSOperation取出来
 - 将取出的NSOperation封装的操作放到一条新线程中执行

###NSOperation的子类

- NSOperation是个抽象类，并不具备封装操作的能力，必须使用它的子类
- 使用NSOperation子类的方式有3种
 - NSInvocationOperation
 - NSBlockOperation
 - 自定义子类继承NSOperation，实现内部相应的方法

###NSInvocationOperation

- 创建NSInvocationOperation对象

```objc
-(id)initWithTarget:(id)target selector:(SEL)sel  object:(id)arg;
//调用start方法开始执行操作
- (void)start;
//一旦执行操作，就会调用target的sel方法

//例
NSInvocationOperation *op = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(run) object:nil];
[op start];
- (void)run
{
    NSLog(@"------%@", [NSThread currentThread]);
}
```
###注意

- 默认情况下，调用了start方法后并不会开一条新线程去执行操作，而是在当前线程同步执行操作
- 只有将NSOperation放到一个NSOperationQueue中，才会异步执行操作

###NSBlockOperation

- 创建NSBlockOperation对象

```objc
+ (id)blockOperationWithBlock:(void (^)(void))block;
//通过addExecutionBlock:方法添加更多的操作
- (void)addExecutionBlock:(void (^)(void))block;
//例

NSBlockOperation *op = [NSBlockOperation blockOperationWithBlock:^{
        // 在主线程
        NSLog(@"下载1------%@", [NSThread currentThread]);
    }];
    
    // 添加额外的任务(在子线程执行)
    [op addExecutionBlock:^{
        NSLog(@"下载2------%@", [NSThread currentThread]);
    }];

[op star];
```

###注意：

- 只要NSBlockOperation封装的操作数 > 1，就会异步执行操作


###NSOperationQueue

- NSOperationQueue的作用
 - NSOperation可以调用start方法来执行任务，但默认是同步执行的
   如果将NSOperation添加到NSOperationQueue（操作队列）中，    系统会自动异步执行NSOperation中的操作

```objc
//添加操作到NSOperationQueue中
- (void)addOperation:(NSOperation *)op;
- (void)addOperationWithBlock:(void (^)(void))block;

// 例1
    // 创建队列
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    // 创建操作
        NSBlockOperation *op1 = [NSBlockOperation blockOperationWithBlock:^{
            NSLog(@"download1 --- %@", [NSThread currentThread]);
        }];
     //添加操作到队列中
        [queue addOperation:op1];

//例2
    // 创建队列
  NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    //    queue.maxConcurrentOperationCount = 2; // 设置最大并发操作数
    queue.maxConcurrentOperationCount = 1; // 就变成了串行队列
    // 添加操作
    [queue addOperationWithBlock:^{
        NSLog(@"download1 --- %@", [NSThread currentThread]);
        [NSThread sleepForTimeInterval:0.01];
    }];

```

###最大并发数

- 什么是并发数
 - 同时执行的任务数
 - 比如，同时开3个线程执行3个任务，并发数就是3

- 最大并发数的相关方法

```objc
- (NSInteger)maxConcurrentOperationCount;
- (void)setMaxConcurrentOperationCount:(NSInteger)cnt;

 NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    // 设置最大并发操作数
 queue.maxConcurrentOperationCount = 1; // 就变成了串行队列
```
###队列的取消、暂停、恢复

- 取消队列的所有操作

```objc
- (void)cancelAllOperations;
提示：也可以调用NSOperation的- (void)cancel方法取消单个操作
 [self.queue cancelAllOperations];
暂停和恢复队列
- (void)setSuspended:(BOOL)b; // YES代表暂停队列，NO代表恢复队列
- (BOOL)isSuspended;
//例子
    if (self.queue.isSuspended) {
        // 恢复队列，继续执行
        self.queue.suspended = NO;
    } else {
        // 暂停（挂起）队列，暂停执行
        self.queue.suspended = YES;
    }
```

###操作依赖

- NSOperation之间可以设置依赖来保证执行顺序
- 比如一定要让操作A执行完后，才能执行操作B，可以这么写
```obj
[operationB addDependency:operationA]; // 操作B依赖于操作A
可以在不同queue的NSOperation之间创建依赖关系
```
###操作的监听

```objc
可以监听一个操作的执行完毕
- (void (^)(void))completionBlock;
- (void)setCompletionBlock:(void (^)(void))block;
```

###自定义NSOperation

- 自定义NSOperation的步骤很简单
 - 重写- (void)main方法，在里面实现想执行的任务

- 重写- (void)main方法的注意点
 - 自己创建自动释放池（因为如果是异步操作，无法访问主线程的自动释放池）
- 经常通过- (BOOL)isCancelled方法检测操作是否被取消，对取消做出响应


```objc
//需要执行的任务
- (void)main
{
    for (NSInteger i = 0; i<1000; i++) {
        NSLog(@"download1 -%zd-- %@", i, [NSThread currentThread]);
    }
    if (self.isCancelled) return;
}
```










