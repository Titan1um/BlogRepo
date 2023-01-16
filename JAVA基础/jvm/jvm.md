## -反射为什么慢
 1. method.invoke中每次都要进行参数数组包装
 2. method.invoke中要进行方法可见性检查
 3. accessor的java实现方式下，invoke会检查参数的类型匹配

 优化思路：

 jdk6：新增inflation机制，每个反射方法调用时都会生成一个委托，委托将反射调用的执行分成`native方式`和`java bytecode方式`，并配合计数器计算方法执行次数以根据频率来切换两种方式
- count < 15：使用本地方法进行反射调用
- count > 15：为方法内容生成java字节码，无需陷入内核态调用本地方法

jdk7：使用不可变的methodhandle，调用时不再检查参数匹配
- 可以采取更为激进的内联优化
- 减少了参数匹配检查

> [请问Java反射的性能为什么比直接调用慢一个数量级左右？](https://www.zhihu.com/question/30097357)


## -Class.forName 和 ClassLoader.loadClass
