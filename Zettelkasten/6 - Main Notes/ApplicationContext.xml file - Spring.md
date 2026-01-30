
09-10-2025 18:38

Status: #baby 

Tags: [[Spring]]

---
# ApplicationContext.xml file - Spring

храним в resources



---
### Пример ApplicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context.xsd">
	
	<!-- Автоматическое сканирование компонентов в пакете -->
	<context:component-scan base-package="com.example.demo"/>
	
	<!-- добавляет общий BeanPostProcessor для обработки method-level аннотаций бинов, типа @Autowired, @PostConstruct и т.д. -->
	<bean class = "org.springframework.context.annotation.CommonAnnotationBeanPostProcessor"/>
	
	<bean id="classicalMusic" class="secret.boy.spring.demo.ClassicalMusic" scope="singleton" />
	<bean id="rockMusic" class="secret.boy.spring.demo.RockMusic"/>
	
	<bean id="musicPlayer" scope="prototype" class="secret.boy.spring.demo.MusicPlayer">  
	    <property name="music">  
	        <list>            
		        <ref bean="classicalMusic"/>  
	            <ref bean="rockMusic"/>  
	            <ref bean="grungeMusic"/>  
	        </list>    
		</property>    
		<property name="name" value="${musicPlayer.name}"/>  
	    <property name="volume" value="${musicPlayer.volume}"/>  
	</bean>
	
</beans>
```

> [!note]
Для инъекции констант удобно использовать [[property file - Spring|property file]]
>
> В ApplicationContext он указывается так:
>```xml
><context:property-placeholder location="classpath:musicPlayer.properties"/>
>```
>

> [!warning] **Можно явно определить тип аргумента, который мы инжектим:**
> ```xml
> <bean name="jedis" class="redis.clients.jedis.JedisPooled"  
 > destroy-method="close">  
>	<constructor-arg index="0" type="java.lang.String" value="${redis.host}"/> 
>	<constructor-arg index="1" type="int" value="${redis.port}"/>  
></bean>
> ```


~={red}!!!=~ При инициализации контекста через XML ~={cyan}инъекция через поле НЕ РАБОТАЕТ=~:
- пытаемся инжектить через property -> он ищет setter, если setter-a нет - кидает ошибку. ~={red}!!!=~

#### Включение class-level аннотаций в xml

 Чтобы работали базовые аннотации Spring (~={purple}@PreDestroy=~, ~={purple}@PostConstruct=~ и ~={purple}@Resource=~ (не class-level, для этого есть context:component-scan)), добавляем в applicationContext.xml bean общего [[Про использование BeanPostProcessor - Spring|BeanPostProcessor]]:
 ```xml
    <bean class = "org.springframework.context.annotation.CommonAnnotationBeanPostProcessor"/>
 ```

Для ~={purple}@Autowired=~ существует свой AutowiredAnnotationBeanPostProcessor:
 ```xml
    <bean class = "org.springframework.context.annotation.AutowiredAnnotationBeanPostProcessor"/>
 ```
#### xml namespaces

Т.к. вручную добавлять каждый обработчик анотаций заёбно - сделали namespaces, которые включают в себя несколько аннотаций:

- `context:component-scan` - добавляет обработчики class-level аннотаций (~={purple}@Controller=~, ~={purple}@Component=~ и т.д.)
	~={red}!!!=~ ~={yellow}При использовании `component-scan` все user-defined классы пакета, реализующие BeanPostProcessor, будут загружены автоматически.=~ ~={red}!!!=~ 

- `context:annotation-config` - добавляет обработчики method-level аннотаций (~={purple}@PostConstruct=~, ~={purple}@PreDestroy=~, ~={purple}@Autowired=~ и т.д.)

---

## Способы внедрения зависимостей

#### Через конструктор

```java
public MusicPlayer(Music music, String name) {
	this.music = music;
	this.name = name;
}
```
	
```xml
<bean id="musicBean" class="secret.boy.spring.demo.ClassicalMusic"/>
<bean id="MusicPlayer" class="secret.boy.spring.demo.MusicPlayer">
	<constructor-arg ref="musicBean"/>
	<constructor-arg value="some name"/>
</bean>
```

> [!note] 
> Spring XML позволяет явно указать, **какому параметру конструктора** соответствует каждое значение.
>1. указывать аргументы по порядку (как записаны параметры в конструкторе)
>2.  указывать аргументы с индексом
>3.  указывать имя праметра:
>```java
>public UserService(String name, int age) { ... }
>```
>```xml
><bean id="userService" class="com.example.UserService">
>    <constructor-arg name="name" value="John"/>
 >   <constructor-arg name="age" value="42"/>
></bean>
>```



#### Через setter

```java
public class MusicPlayer {  
    private Music music;  
    private String name;  
    private int volume;  
	  
    public MusicPlayer(Music music) {  
       this.music = music;  
    }  
	  
    public void setName(String name){  
       this.name = name;  
    }  
	  
    public void setVolume(int volume){  
       this.volume = volume;  
    }  
	  
    public void play() {  
       System.out.println(this.music.getSong());  
    }  
      
}
```
	
```xml
<bean id="musicBean" class="secret.boy.spring.demo.ClassicalMusic"/>
<bean id="musicPlayer" class="secret.boy.spring.demo.MusicPlayer">  
    <constructor-arg ref="classicalMusic"/>  
    <property name="name" value="Some name"/>  
    <property name="volume" value="50"/>  
</bean>
```

> [!warning]
> ```xml
>     <property name="volume" value="50"/>  
> ```
> Тут name сопоставляется с именем сеттера, а не поля:
> ```java
> public void setVolume(int volume){
> 	this.someField = volume
> } 
> ```



## Подключение property файла

```xml
<context:property-placeholder location="classpath:musicPlayer.properties"/>
```

[[Classpath - Java|Что такое classpath]]

Использование значений из property файла для инъекции:
```xml
<property name="name" value="${musicPlayer.name}"/> 
<property name="volume" value="${musicPlayer.volume}"/> 
<!-- в musicPlayer.properties эти переменные так и названы:
 musicPlayer.name=Some name
 musicPlayer.volume=70
 -->
```


# Типы задаваемых значений

- **value**
```xml
    <property name="name" value="Some name"/>  
```

- **ref** - сссылка
```xml
    <property name="music" ref="classicalMusic"/>  
```

- **list**
```java
public void setServers(List<String> servers) {
	this.servers = servers;
}
```

```xml
        <property name="servers">
            <list>
                <value>https://server1.com</value>
                <value>https://server2.com</value>
                <value>https://server3.com</value>
            </list>
        </property>
    </bean>
```
	в list могут быть и value и ref:
	
```java
    public void setSenders(List<EmailSender> senders) {
        this.senders = senders;
    }
```
	
```xml
<bean id="mailService" class="com.example.MailService">
    <property name="senders">
        <list>
            <ref bean="sender1"/>
            <ref bean="sender2"/>
        </list>
    </property>
</bean>
```


- **set** - аналогично list

- **map** 
- **props**



> [!warning] ~={red}**Biggest Ever Warning**=~
> Если при инициализации указать параметр `lazy-init= "true"`, то экземпляр бина не будет создан сразу же при инициализации контекста.
>
>```xml
><bean id = "goodVillain" class = "film.Villain" lazy-init= "true">          
>	<property name = "name" value = "Good Vasily"/>        
></bean>
>```
>P.S. имеет смысл только для singleton бинов.





----
#### [[ApplicationContext.xml file - Spring - Flashcards|Link to flashcards]]



---
### References:

- [[property file - Spring]]
- [[yaml file - Spring]]