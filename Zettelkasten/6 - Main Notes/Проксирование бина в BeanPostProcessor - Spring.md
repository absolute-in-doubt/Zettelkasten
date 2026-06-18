
01-11-2025 12:07

Status: #baby 

Tags: [[Spring]]

---
# Проксирование бина в BeanPostProcessor - Spring

### Зачем?

##### **Добавление поведения вокруг метода**

Например:

- Логирование

- Транзакции (**[[@Transactional - Jakarta & Spring ORM|@Transactional]]**)
   
- Безопасность (**[[@PreAuthorize vs @Secured vs @RolesAllowed - Spring Security|@Secured]]**)
   
- Кэширование (**[[@Cacheable - Spring Data|@Cacheable]]**)
   
- Метрики, аудит, трассировка
   

Это делается через **обёртку**: прокси перехватывает вызов метода и добавляет нужную логику до/после/вместо.

> [!warning]
> По конвенциям Spring, те BeanPostProcessor, которые что-то в классе меняют (**создают прокси**), должны это делать не на этапе postProcessBeforeInitialization, а на этапе postProcessAfterInitialization.
> 
> Это нужно, чтобы все аннотации были корректно обработаны соответствующими постпроцессорами и мы не тащили их в dynamic proxy.


#####  **Изменение поведения без доступа к исходному классу**

Если бин — сторонний класс (например, из библиотеки), ты не можешь его изменить напрямую. Но можешь создать прокси, который будет вести себя иначе.

### Как?

Есть два подхода:
- **dynamic proxy** — имплементировать те же самые интерфейсы. 
- **CGLib** — создание наследника и переопределение его методов.

В принципе, ~={cyan}CGLib считается хуже=~. Отчасти он уступает в производительности, но главная проблема а в различных ограничениях: вы не от любого класса можете наследовать, есть final классы и методы.


~={cyan}Spring AOP=~ работает именно через прокси, и если спринговому аспекту нужно сделать прокси на какой-то объект, он сначала смотрит, есть ли у него интерфейсы. Если есть, он идет через dynamic proxy, если нет, он идет через CGLib.


---
## Проксирование на практике


Т.к. у класса может быть множество аннотаций -> в метод `postProcessAfterInitialization` может прийти уже proxy, созданный другим BeanPostProcessor (тем, который вызвал `postProcessAfterInitialization` до нашего). 

Для того, чтобы знать изначальный тип объкта, его можно сохранять при вызове `postProcessBeforeInitialization`:

```java
public class ProfilingHandlerBeanPostProcessor implements BeanPostProcessor {
	private Map<String, Class> map = new HashMap<>();   
	private MyProfilingController controller = new MyProfilingController(); 
	@Override    
	public Object postProcessBeforeInitialization(Object bean, 
	   String beanName) throws BeansException {
	   Class<?> beanClass = bean.getClass();        
	   if (beanClass.isAnnotationPresent(Profiling.class)) {
		   map.put(beanName, beanClass);        
	   }        
	return bean;    
	}
	
	@Override 
	public Object postProcessAfterInitialization(Object bean, String beanName)  throws BeansException {
		Class beanClass = map.get(beanName);    
		if (beanClass != null) {        
		   return Proxy.newProxyInstance(
			   beanClass.getClassLoader(),
			   beanClass.getInterfaces(),
			   new InvokationHandler() { //реализация прокси-метода. Применяется к каждому методу класса
				   @Override
				   public Object invoke(Object proxy, Method method, Object[] args) throws ... {
					   if(controller.isEnabled()){
						   System.out.println("Started profiling");
						   long startTime = System.nanoTime();
						   Object retVal = method.invoke(bean, args);
						   System.out.println("Finished. Execution time = " + System.nanoTime() - startTime);
						   return retVal;
					   } else
						   return method.invoke(bean, args);
				   }
			   }
			   
		   );    
		}    
		return bean;
	}
 }
```

`Proxy.newProxyInstance()` - создаёт прокси на лету. Туда передаём ClassLoader, список интерфейсов начального бина и реализацию прокси-методов.

`InvokationHandler` - реализация прокси-метода. Применяется к каждому методу класса


----
#### [[Изменение поведения бина в BeanPostProcessor - Spring - Flashcards|Link to flashcards]]



---
### References:

- [[Про использование BeanPostProcessor - Spring]]