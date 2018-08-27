#### 测试场景
BeanPostProcessor.postProcessAfterInitialization(Object bean, String beanName)。实现接口FactoryBean被初始化两次。
#### 分析
- 第一次初始化是对实例FactoryBean的初始化
- 第二次初始化是对实例FactoryBean.getObject()真正对象的初始化
#### 举例
> MyBatis CoreMapperFactoryBean实现FactoryBean接口。  

>Spring扫描testUserMapper，见代码：
```java
class:org.springframework.beans.factory.support.AbstractBeanFactory

if (mbd.isSingleton()) {
	sharedInstance = getSingleton(beanName, new ObjectFactory<Object>() {
		@Override
		public Object getObject() throws BeansException {
			try {
				return createBean(beanName, mbd, args);
			}
			catch (BeansException ex) {
				// Explicitly remove instance from singleton cache: It might have been put there
				// eagerly by the creation process, to allow for circular reference resolution.
				// Also remove any beans that received a temporary reference to the bean.
				destroySingleton(beanName);
				throw ex;
			}
		}
	});
	bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
}
````
> 调用方法createBean()返回的结果sharedInstance是CoreMapperFactoryBean对象实例，调用getObjectForBeanInstance()返回的结果是真实的对象testUserMapper实例，见代码：
```java
protected Object getObjectForBeanInstance(
			Object beanInstance, String name, String beanName, RootBeanDefinition mbd) {

	// Don't let calling code try to dereference the factory if the bean isn't a factory.
	if (BeanFactoryUtils.isFactoryDereference(name) && !(beanInstance instanceof FactoryBean)) {
		throw new BeanIsNotAFactoryException(transformedBeanName(name), beanInstance.getClass());
	}

	// Now we have the bean instance, which may be a normal bean or a FactoryBean.
	// If it's a FactoryBean, we use it to create a bean instance, unless the
	// caller actually wants a reference to the factory.
	if (!(beanInstance instanceof FactoryBean) || BeanFactoryUtils.isFactoryDereference(name)) {
		return beanInstance;
	}

	Object object = null;
	if (mbd == null) {
		object = getCachedObjectForFactoryBean(beanName);
	}
	if (object == null) {
		// Return bean instance from factory.
		FactoryBean<?> factory = (FactoryBean<?>) beanInstance;
		// Caches object obtained from FactoryBean if it is a singleton.
		if (mbd == null && containsBeanDefinition(beanName)) {
			mbd = getMergedLocalBeanDefinition(beanName);
		}
		boolean synthetic = (mbd != null && mbd.isSynthetic());
		object = getObjectFromFactoryBean(factory, beanName, !synthetic);
	}
	return object;
}
````
> spring对非FactoryBean接口判断，如果不是直接返回，如果是取真实对象。

#### 测试结果
```java
com.ztesoft.zsmart.core.jdbc.mybatis.mapper.CoreMapperFactoryBean@43730ad7================>testUserMapper
org.apache.ibatis.binding.MapperProxy@1aeed8ce================>testUserMapper
````