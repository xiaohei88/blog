#### 问题
> 关于嵌套事务，当事务传播类型为Propagation.REQUIRED，事务的属性无效的原因。

#### 分析
> Spring官网对PROPAGATION_REQUIRED描述为：如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。这是最常见的选择。

> 调试Spring源码发现，第一次打开事务，会走doBegin方法，这里会取timeout设置到当前事务对象里。第二次打开事务，会判断是否是嵌套事务，如果是直接继承第一事务的，不会进入doBegin方法。

##### 事务传播行为类型
事务传播行为类型  | 说明
---|---
PROPAGATION_REQUIRED  | 如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。这是最常见的选择
PROPAGATION_SUPPORTS  | 支持当前事务，如果当前没有事务，就以非事务方式执行
PROPAGATION_MANDATORY |	使用当前的事务，如果当前没有事务，就抛出异常
PROPAGATION_REQUIRES_NEW |	新建事务，如果当前存在事务，把当前事务挂起
PROPAGATION_NOT_SUPPORTED |	以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
PROPAGATION_NEVER |	以非事务方式执行，如果当前存在事务，则抛出异常。
PROPAGATION_NESTED |	如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION_REQUIRED类 似的操作。