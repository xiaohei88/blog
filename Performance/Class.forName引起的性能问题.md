#### 问题
> 通过测试工具发一万次请求，发现每次执行Class.forName耗时间

#### 分析
- 打开JDK自带工具[VisualVM]   
- 可以查看本地JAVA进程，也可以[通过JMX查看远程JAVA进程](http://note.youdao.com/noteshare?id=2f7c0f04594534d0ff0f56b55945b1d8)
- 运行程序，查看VisualVm-Sampler/Profiler，进行分析。
- 查看代码调用链及调用时间

#### 原因
> 如果只执行一次Class.forName本身不会耗费很多性能。但是在1万次请求下都要执行这个方法，这个时候性能就出来了。

#### 结果
> 将调用Class.forName放到启动时，这样可以防止每次request，都调用forName方法