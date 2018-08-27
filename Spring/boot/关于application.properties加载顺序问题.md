##### 工程结构
Project  | Properties
---|---
A  | application.properties
B  | application.properties
C  | application.properties

##### 说明
> A工程依赖B和C

##### 分析
- 如果A工程下有application.properties文件，那么B和C工程下的application.properties就不加载    
- 如果A工程下没有，那么根据JAR包名字顺序加载对应的application.properties

##### 代码
> class:org.springframework.boot.context.config.ConfigFileApplicationListener
```
private PropertySource<?> loadIntoGroup(String identifier, String location, Profile profile) throws IOException {
	Resource resource = this.resourceLoader.getResource(location);
	... ...
}
```


