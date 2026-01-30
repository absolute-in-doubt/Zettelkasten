
01-11-2025 09:13

Status: #baby 

Tags: [[Spring]]

---
# Инициализация контекста через XML - Spring

Для создания контекста из XML используем `ClasspathXmlApplicationContext`:

```java
public class Main {
    public static void main(String[] args) {
		ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("context.xml");
		context.getBean(TerminatorQuoter.class).sayQuote();
	}
}
```

1. **XmlBeanDefinitionReader** на входе получает InputStream и загружает _Document_ через _DefaultDocumentLoader_:  

```java
Document doc = doLoadDocument(inputSource, resource);
return registerBeanDefinitions(doc, resource);
```

2.  После этого каждый элемент этого документа обрабатывается и, если он является бином, создается **BeanDefinition** на основе заполненных данных (id, name, class, alias, init- method, destroy-method и др.):  
  
```java
} else if (delegate.nodeNameEquals(ele, "bean")) { 
   this.processBeanDefinition(ele, delegate);
```

  
3.  Каждый BeanDefinition помещается в Map, который хранится в классе **DefaultListableBeanFactory**:  

```java
this.beanDefinitionMap.put(beanName, beanDefinition);
this.beanDefinitionNames.add(beanName);
```

  
В коде Map выглядит следующим образом:  

```java
/** Map of bean definition objects, keyed by bean name */
private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<String, BeanDefinition>(64);
```

 В этой map у нас ID бина соответствует его декларации, в которую входит:
	- из какого класса его надо создавать,
	- есть ли у него init-метод и как он называется,    
	- какие у него проперти,
	- и все другие подробности бина, прописанные в XML.




1.  Когда BeanDefinitions созданы, BeanFactory начинает по ним работать:
	- создаёт из классов объекты и складывает их в контейнер
	- ~={cyan}BeanPostProcessor-ы тоже создаются BeanFactory, но она "знает", что это не обычные бины.=~
> [!note] 
> Если bean является singleton, то он создаётся изначально, как только поднимается контекст. А все прототайпы создаются в тот момент, когда они нужны. Кто-то запросил прототайп — тогда Spring его создал, настроил, отдал и забыл про него.

> [!warning] 
> Если вы, например, прописываете ~={cyan}destroy-метод=~ для бина, то ~={cyan}для синглтона этот метод работать будет=~, а~={cyan} для прототайпа — нет=~. Когда контекст закрывается, Spring проходит по всем бинам, которые там хранятся (а это только синглтоны), находит их destroy-методы, если они прописаны, и запускает. А прототайпы Spring нигде не хранит, и поэтому destroy-метод для них работать не будет.

2. Включается BeanPostProcessor. Он позволяет настраивать наши бины до того, как они попали в контейнер. [[Про использование BeanPostProcessor - Spring|Про использование BeanPostProcessor]]. У этого интерфейса есть два метода:

- postProcessBeforeInitialization (Object bean, String beanName) Вызывается до init-метода

- postProcessAfterInitialization (Object bean, String beanName) Вызывается после init-метода
   
4. Между вызовами методов BeanPostProcessor-ов вызывается init-method. В init-method обычно ставится логика инициализации класса, где требются injected beans. Т.к.~={cyan} **init-method выполняется после того, как все зависимости (бины) уже injected into the class.**=~



---

**TL; DR:**
_Все начинается с того, что  
- BeanDefinitionDocumentReader прочитал наш XML,  
- вытащил BeanDefinition,  
- BeanFactory вытащил из этих BeanDefinition определения BeanPostProcessor-ов, создал их и положил в сторонку — он знает, что с его точки зрения это необычные бины, с их помощью он потом будет настраивать все остальные бины.

Поэтому все это выглядит так: бин прошел первые этапы настройки с помощью BeanPostProcessor, после этого у данного бина вызвался init-метод (отработал PostConstruct), и потом идет еще один проход. В конце у нас появляются полностью настроенные при помощи BeanPostProcessor объекты.

![[Pasted image 20251101114507.png]]


----
#### [[Инициализация контекста через XML - Spring - Flashcards|Link to flashcards]]



---
### References:

- [[Bean scope - Spring]]
- [[Bean Lifecycle - Spring]]