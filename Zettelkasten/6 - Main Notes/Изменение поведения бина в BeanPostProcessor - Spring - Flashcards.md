
Theory for the cards: [[Проксирование бина в BeanPostProcessor - Spring]]

FILE TAGS: spring spring-ripper

Q: Какие есть методы в интерфейсе BeanPostProcessor в Spring? Для чего нужно два метода? Чем отличается их логика? Когда они вызываются?
A:   
```java
public interface BeanPostProcessor { 
	   public Object postProcessBeforeInitialization(Object bean, 
		   String beanName) throws BeansException;
	
	 public Object postProcessAfterInitialization(Object bean, String beanName)  throws BeansException;
}
```
	
- `postProcessBeforeInitialization` вызывается после конструктора, но до init метода. Здесь мы обрабатываем аннотации, которые не требуют создания прокси (типа инъекции бинов, значений из .property файла). - **работает на оргинальный метод, до того, как все прокси накрутились на него**
	
- `postProcessAfterInitialization` вызывается после init метода. Здесь обрабатывают те аннотации, которые требуют создания прокси.
<!--ID: 1769324948357-->


Q: Какие способы создания прокси есть в Spring AOP? 
A:   
- CGLib - расширение класса
- dynamic proxy - реализация всех интерфейсов класса
	
CGLib медленнее + не все классы могут быть расширяемы и не все методы наследуемы.
	
По умол чанию Spring сначала пытается создать dynamic proxy, и только если не находит  интерфейсов у класса, пытается создать прокси с помощью CGLib.
<!--ID: 1769324948365-->

Q: Почему нельзя инжектить бины по классу? В каких случаях этп приведет к ошибке `NoSuchBeanException`?
A: Если класс или метод класса отмечены аннотациями, требующими создания динамических прокси, то в `postProcessAfterInitialization` будет создано прокси вместо начального класса.
	
```java
//И, например тут объект будет не найден:
context.getBean(TerminatorQuoter.class); //поиск по классу
	
//а тут будет найден корректно:
context.getBean(Quoter.class); //т.к. Quoter - интерфейс, который реализует и прокси и сам TerminatorQuoter
```
	
В контексте такой прокси будет выглядеть примерно так: `com.sun.proxy.$Proxy7`
<!--ID: 1769342491453-->

