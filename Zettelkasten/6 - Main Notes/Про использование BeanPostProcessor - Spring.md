
01-11-2025 09:32

Status: #baby 

Tags: [[Spring]]

---
# Про использование BeanPostProcessor - Spring

![[Pasted image 20251101094858.png]]

(afterPropertiesSet - устаревший метод из Spring 2, то же самое, что и init-method и @PostConstruct)

![[Pasted image 20251101122152.png]]

~={green}При загрузке класса в контекст вызываются ВСЕ `BeanPostProcessor`-ы, которые мы загрузили. Внутри каждый `BeanPostProcessor` сам проверяет, стоит ли ему что-то сделать, например:=~
```java
beanClass.isAnnotationPresent(Profiling.class)
```

---

BeanPostProcessor позволяет настраивать наши бины до того, как они попали в контейнер. Есть такой паттерн проектирования — chain of responsibility. Он здесь как раз задействован.

Посмотрим, что я могу сделать. Я сейчас хочу кастомизировать Spring, обучить его собственным аннотациям и написать BeanPostProcessor, который будет что-то подкручивать в бине при его создании.

Представьте себе, что я пишу приложение, в котором есть очень много генераций случайных чисел. И я хочу, чтобы эти случайные значения записывались в поля. Я вижу, что в моей команде каждый делают по-своему: кто-то использует `Math.random()`, кто-то `java.util.Random.nextInt()`, кто-то библиотеку скачал.

### Создание свой аннотации бина

Я говорю: «Так, это никуда не годится. Давайте делать это декларативно, мы сейчас придумаем аннотацию ~={purple}@InjectRandomInt=~ и будем ставить её над теми полями, куда нам нужно инжектнуть случайное число. Затем мы научим Spring относиться к этой аннотации, и в момент создания бина настраивать его с учетом этой аннотации».

Давайте это реализуем. Начнем с аннотации. Пойдем в наш класс TerminatorQuoter, добавим там поле `int` под названием `repeat`: сколько раз надо повторять цитату. И я хочу, чтобы это поле задавалось аннотацией InjectRandomInt, которую мы прямо сейчас придумаем. У неё будет два параметра, min и max (для примера дадим им значения 2 и 7). А в методе `sayQuote()` добавим цикл с соответствующим числом итераций:

```java
public class TerminatorQuoter implements Quoter {
    @InjectRandomInt(min = 2, max = 7)    
    private int repeat;    
    private String message;    \
    public void setMessage(String message) {
	    this.message = message;
	}
	@Override    
	public void sayQuote() {    
	    for (int i = 0; i < repeat; i++) {     
			System.out.println("message = " + message);
		}
	}
```

Теперь нужно создать саму аннотацию, IDEA в контекстном меню уже предлагает это сделать. При создании аннотаций очень важно не забыть поменять retention policy на RUNTIME:

```java
import …
@Retention(RetentionPolicy.RUNTIME)
public @interface InjectRandomInt {
 …
}
```

По умолчанию там стоит ~={purple}CLASS=~. Всего есть три варианта:

- ~={purple}**SOURCE**=~ говорит о том, что эта аннотация видна исключительно в исходниках, а когда вы компилируете, в байткоде уже ничего не будет. Например, overwrite — это аннотация такого плана. Она нужна только в процессе разработки кода.
    
- ~={purple}**CLASS**=~ говорит, что аннотация в байткод попасть должна, но при этом все равно через reflection в рантайме вы ее считать не сможете, ее там не будет. Это нужно для вещей вроде AST-трансформаций и инструментирования байткода.
    
- ~={purple}**RUNTIME**=~ стоит у большинства аннотаций, которые видны в рантайме, их можно считать через reflection. Поэтому первое, что я сделал — в настройках поменял дефолтный вариант на RUNTIME, поскольку других не пишу обычно.
    

Мы уже сказали, что у данной аннотации есть два параметра, их мы тоже отмечаем.

```java
import …
@Retention(RetentionPolicy.RUNTIME)
public @interface InjectRandomInt {
 	int min();	
 	int max();
```

Теперь всё компилируется. Но когда я запущу всё это дело, в консоли будет пусто, потому что никто не знает аннотацию «InjectRandomInt». Соответственно, мой параметр repeat — это ноль, и цитата терминатора напечатается ноль раз. Достаточно логично.

### Создаём реализацию BeanPostProcessor для обработки нашей user-defined аннотации

Теперь мы будем обучать. Мы сейчас создадим класс, который имплементирует интерфейс, который будет отвечать за обработку всех бинов, классы которых имеют эту аннотацию хотя бы в каком-то поле. Называться он будет «InjectRandomIntAnnotationBeanPostProcessor». Ну, вы в курсе, что при придумывании внутренних компонентов Spring класс с названием меньше 20 букв — это просто несерьезно! Я даже не шучу, для обработки ~={purple}@Autowired=~ в Spring вполне есть AutowiredAnnotationBeanPostProcessor, так что я тут просто соблюдаю конвенцию.

Этот наш класс имплементирует интерфейс BeanPostProcessor. У этого интерфейса нужно имплементировать два метода:

- postProcessBeforeInitialization (Object bean, String beanName) Вызывается до init-метода
    
- postProcessAfterInitialization (Object bean, String beanName) Вызывается после init-метода
    

О том, почему их два и нельзя было ограничиться одним, поговорим чуть позже.


Оба метода имеют одинаковую сигнатуру: в метод придет бин и его имя. Соответственно, этот метод вызовется для каждого бина. В этом методе я могу вернуть какой-то объект. Теоретически, могу вернуть вообще не тот, который мне дал BeanFactory. Но пока что мы не станем извращаться, будем возвращать в обоих методах полученный объект bean.

Однако, допустим, я хочу в методе postProcessBeforeInitialization взять бин, вытащить его класс с помощью `bean.getClass()`, вытащить все его поля с помощью `getDeclaredFields()` и пройтись по ним. У каждого поля мы постараемся вытащить аннотацию с названием «InjectRandomInt.class». Проверим: если эта аннотация не равна null, значит, она над соответствующим полем стояла, и тогда мне из неё надо вытащить min и max. Затем нам нужно сгенерировать случайное число между min и max.

```java
public class InjectRandomIntAnnotationBeanPostProcessor implements BeanPostProcessor {
    @Override
	public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		Field[] fields = bean.getClass().getDeclaredFields();   
		 for (Field field : fields) {       
			  InjectRandomInt annotation = field.getAnnotation(InjectRandomInt.class);            
			  if (annotation != null) {           
				 int min = annotation.min();                
				 int max = annotation.max();                
				 Random random = new Random();                
				 int i = min + random.nextInt(max - min);                
			}            
		}        
		 return bean;    
	}    
	 
	 @Override    
	 public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {        
		 return bean;    
	 }
 }
```

Здесь i будет случайным числом, которое мне теперь нужно поместить в поле. Для этого требуются две вещи. Во-первых, если код написан правильно, то поле будет private, так что надо указать `field.setAccessible(true)`. Второе — мы могли бы просто написать `field.set(i)`, **но мы так делать не будем**. Потому что в этом случае нам придется обрабатывать исключения. Поскольку мы имплементируем чужой интерфейс, если он не кидает исключения, то и мы не можем сделать `throws`. А постоянные `try` и `catch` плохо сказываются на нервной системе.

Поэтому мы воспользуемся прекрасной библиотекой, которая есть у Spring — ReflectionUtils, которая умеет делать обычные reflections, которые вы знаете, только без try и catch (на самом деле, библиотека просто оборачивает их и прячет в RuntimeException). В этом случае метод set.field принимает на один параметр больше: я должен указать, для какого поля я буду давать значение (field), затем для какого объекта его нужно будет засунуть (для моего bean), и наконец, само значение (это i). Вот и всё, никаких try и catch.

```java
public class InjectRandomIntAnnotationBeanPostProcessor implements BeanPostProcessor {
    @Override    
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {        
	    Field[] fields = bean.getClass().getDeclaredFields();    
		for (Field field : fields) {        
		    InjectRandomInt annotation = field.getAnnotation(InjectRandomInt.class);            
		    if (annotation != null) {             
			    int min = annotation.min();                
			    int max = annotation.max();                
			    Random random = new Random();                
			    int i = min + random.nextInt(max - min);
				field.setAccessible(true);                
			    ReflectionUtils.setField(field, bean, result);            
			}        
		}        
		return bean;    
	}    
	
	@Override    
	public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {        
		return bean;    
	}
}
```


### Добавление user-defind BeanPostProcessor в контекст

У Spring в этом плане всё очень удобно: практически любую вещь, которую вы хотите добавить в Spring, вы прописываете в контекст. Причем у нас есть разные варианты: можно в XML, если мы работаем с ним, можно в Java config, можно через аннотации.

Мы сейчас работаем с XML. Вот я указываю бин из класса «InjectRandomInt».

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean class = "quoters.InjectRandomIntAnnotationBeanPostProcessor"/>
	
	<bean class = "quoters.TerminatorQuoter" id = "terminatorQuoter" >
		<property name="message" value="I'll be back"/>
	</bean>
	
</beans>   
```

ID я ему давать не буду: это инфраструктурный бин, а значит, инжектить мы его никуда не будем. Конечно, Spring придумает ему какой-то ID, но мне это даже неинтересно.

Теперь мы запускаем то же самое приложение. Каждый раз при создании бина ему в поле `repeat` будет инжектиться рандомное значение в заданных пределах.

> [!note]
> Чтобы работали базовые аннотации Spring (@PreDestroy, @PostConstruct и @Resource (не class-level, для этого есть context:component-scan)), добавляем в [[ApplicationContext.xml file - Spring|applicationContext.xml]] bean общего BeanPostProcessor:
> ```xml
>    <bean class = "org.springframework.context.annotation.CommonAnnotationBeanPostProcessor"/>
> ```
> 
> Удобнее всего добавлять обработчики аннотаций через [[ApplicationContext.xml file - Spring#xml namespaces|xml namespaces]].


## [[Изменение поведения бина в BeanPostProcessor - Spring|Изменение поведения бина в BeanPostProcessor]]


----
#### [[Про использование BeanPostProcessor - Spring - Flashcards|Link to flashcards]]



---
### References:

