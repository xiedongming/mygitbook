## 九宫格计算思路
- 利用控件的索引index计算出控件所在的行号和列号
- 利用列号计算控件的x值 
- 利用行号计算控件的y值

## HUD
- 其他说法：指示器、遮盖、蒙板
- 半透明HUD的做法
    - 背景色设置为半透明颜色

## 定时任务
- 方法1：performSelector

```objc
// 1.5s后自动调用self的hideHUD方法
[self performSelector:@selector(hideHUD) withObject:nil afterDelay:1.5];
```
- 方法2：GCD

```objc
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
    // 1.5s后自动执行这个block里面的代码
    self.hud.alpha = 0.0;
});
```
- 方法3：NSTimer

```objc
// 1.5s后自动调用self的hideHUD方法
[NSTimer scheduledTimerWithTimeInterval:1.5 target:self selector:@selector(hideHUD) userInfo:nil repeats:NO];
// repeats如果为YES，意味着每隔1.5s都会调用一次self的hidHUD方法
```

## 常见问题
- 项目里面的某个.m文件无法使用
    - 检查：Build Phases -> Compile Sources
- 项目里面的某个资源文件（比如plist、音频等）无法使用
    - 检查：Build Phases -> Copy Bundle Resources

## 模型
- 什么是模型？
    - 专门用来存放数据的对象
    - 一般都是一些直接继承自NSObject的纯对象
    - 内部会提供一些属性来存放数据
    - 字典转模型的过程最好封装在模型内部
    - 模型应该提供一个可以传入字典参数的构造方法
   ```objc
     - (instancetype)initWithDict:(NSDictionary *)dict;
      +(instancetype)xxxWithDict:(NSDictionary *)dict;
    ~~~


## 一个控件看不见有哪些可能？

- 宽度或者高度其实为0
- 位置不对（比如是个负数或者超大的数，已经超出屏幕）
- hidden == YES
- alpha <= 0.01
- 没有设置背景色、没有设置内容
- 可能是文字颜色和背景色一样
## Instancetype

- instancetype在类型表示上，跟id一样，可以表示任何对象类型
- instancetype只能用在返回值类型上，不能像id一样用在参数类型上
- instancetype比id多一个好处：编译器会检测instancetype的真实类型


##自定义控件
 - 比如自定义UIview
 - 新建一个类继承UIView
 - 重写构造方法
 ```objc
  -(instancetype)init
 ```
 