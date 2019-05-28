
## ISSUE

1. idea maven 依赖红线

案例：
pom.xml修改为自己的父级项目
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.9.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

父级项目pom.xml 


```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>${spring-boot.version}</version>
    <type>pom</type>
    <scope>provided</scope>
</dependency>
```
```xml
<scope>import</scope> 
```
maven红线报错

```xml
<scope>provided</scope>  
```
maven正常

## 参考看

Introduction to the Dependency Mechanism

https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html

Maven Dependency Scope 说明

https://www.jianshu.com/p/a4fc54b5a6bf

maven中scope属性

https://www.cnblogs.com/hzzll/p/6738955.html


