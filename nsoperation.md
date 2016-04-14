# NSOperation
##简介

###NSOperation的作用
- 配合使用NSOperation和NSOperationQueue也能实现多线程编程
- NSOperation和NSOperationQueue实现多线程的具体步骤
 - 先将需要执行的操作封装到一个NSOperation对象中
 - 然后将NSOperation对象添加到NSOperationQueue中
   系统会自动将NSOperationQueue中的NSOperation取出来
 - 将取出的NSOperation封装的操作放到一条新线程中执行

