
25-01-2026 15:05

Status:

Tags: [[Spring]]

---
# ApplicationListener - Spring

- ContextStartedEvent - контекст только начал своё построение
- ContextStoppedEvent
- **ContextRefreshedEvent** - контекст закончил своё построение, но до запуска lifecycle-компонентов.
- ContextClosedEvent

Из каждого event можно получить контекст

~={red}**Можно использовать аннотацию ~={purple}@EventListener=~ ~={yellow}вместо=~ интерфейса.**=~ -> просто метод.

---

### 3rd phase of constructor

Выполняется, когда все бины загржены в контекст, а значит, выполнились:
1. конструкторы
2. `beforInitialisation` метод постпроцессора - injections
3. `init`-методы бинов
4. `afterInitialization` метод постпроцессора - создание прокси


Тут мы добавили вызов методов, аннотированных нашей custom аннотацией @AfterProxy.
Выполняется всё это когда контекст загружен и вызвался event **ContextRefreshedEvent**. Подразумевается, что аннотацию @AfterProxy ставт на не init-type=lazy бинах. (т.к. они не инстанциируются при загрузке контекста).

**~={purple}@EventListener=~** - method-level annotation

```java
PostProxyInvokerApplicationListener implements ApplicationListener<ContextRefershedEvent> {
	
	@Autowired
	private ConfigurableListableBeanFactory factory;
	//инжектить спринговский класс в нашу реализацию спринговского интерфейса - цэ ОК
	
	@EventListener(classes = ContextRefreshedEvent.class)
	public void onApplicationEvent(ContextRefreshedEvent event){
		ApplicationContext context = event.getApplicationContext();
		String[] names = context.getBeanDefinitionNames();
		for(Stirng name : names){
			//на этом этапе в контексте уже лежат прокси
			// -> ищем по beanDefinitions
			BeanDefinition beanDefinition = factory.getBeanDefinition(name);
			Stirng originalClassName =  beanDefinition.getBeanClassName();
			try {
				Class<?> originalClass = Class.forName(originalClassName);
				for(Method method : originalClass.getMethods()){
					if(method.isAnnotationPresent(AfterProxy.class)){
					//это метод изначального класса, а нам надо запустить метод класса прокси
						Object bean = context.getBean(name);
						//т.к. сигнатуры метода оригинального класса и метода прокси идентичны:
						Method currentMethod = bean.getClass().getMethod(
							method.getName(),
							method.getParameterTypes()
						)
						currentMethod.invoke(bean);
					}
				}
				
			} catch(Exception e){
				e.printStackTrace();
			}
		}
	}
}
```

> [!note]
> Подход с `BeanFactory` - наиболее правильный, т.к. мы проходим имена всех BeanDefinitions, а они могут быть init-type=lazily или вообще prototype. 
> 
>Т.е. в контексте бина может не быть, но есть BeanDefinition.
>



----
#### [[ApplicationListener - Spring - Flashcards|Link to flashcards]]



---
### References:

https://www.baeldung.com/spring-context-events - тут еще инфа 