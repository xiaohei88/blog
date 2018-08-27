#### 问题描述
> 关于MappingJackson2HttpMessageConverter加载顺序的原因，为什么有时候有用，有时候就无效。

#### 分析
> - Spring Boot加载Bean有两种方式：1、@ComponentScan 2、spring.factories。第1种方式优先于第2种方式加载。   
> - 如果业务配置MappingJackson2HttpMessageConverter的配置类是用第1种方式，那么这个类在`AbstractMessageConverterMethodProcessor`-`messageConverters`就会排第一个。对返回值解析时，优先调用，见`AbstractMessageConverterMethodProcessor`-`writeWithMessageConverters`
> - 如果业务配置MappingJackson2HttpMessageConverter的配置类是用第2种方式，那么你所配置的类晚于`WebMvcAutoConfiguration`加载，导致业务配置的MappingJackson2HttpMessageConverter不会排在第一个，处理返回值分析时，会暴露问题，如你的控制器方法返回String，但是加了注解`@ReponseBody`，就会抛出异常。唯一的解决方案是，加注解`@AutoConfigureBefore(WebMvcAutoConfiguration.class)`或者提高优先级`@AutoConfigureOrder((Ordered.HIGHEST_PRECEDENCE))`

#### 案例
> 具体的案例可以见portal的`PortalWebCoreAutoConfiguration`