
26-09-2026 11:02

Status:

Tags: [[Spring AOP]] [[Spring]]

---
# MethodInterceptor - Spring AOP Alliance

`org.aopalliance.intercept.MethodInterceptor`


```
Client
  ↓
Proxy
  ↓
Interceptor(s)
  ↓
Target Bean
  ↓
Target method
```

---
#### Часть иерархии и наследник `Interceptor`

`Interceptor` — более общая marker-абстракция механизма перехвата вызова.

```
org.aopalliance.intercept.Interceptor
                 │
                 ├── MethodInterceptor
                 │       ↓
                 │   invoke(MethodInvocation)
                 │
                 └── другие виды interceptor'ов
```


---

`MethodInterceptor` - интерфейс AOP Alliance, реализация которого определяет логику вокруг вызова метода бина (так-то метод бина может воообще быть не вызван.)

```java
public interface MethodInterceptor extends Interceptor {

    Object invoke(MethodInvocation invocation)
        throws Throwable;
}
```


То есть по сути вот так:

```java
Object invoke(MethodInvocation invocation) { 
	
	// before Object 
	
	result = invocation.proceed(); 
	
	// after return result; 
	
}
```


### Пример

```java
@Override  
public @Nullable Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {  
    Class<?> beanClass = map.get(beanName);  
    if(beanClass != null){  
        ProxyFactory proxyFactory = new ProxyFactory(bean);  
        // Вот здесь: добавляем Advice
        proxyFactory.addAdvice( (MethodInterceptor) invocation -> {  
                    Method method = invocation.getMethod();  
                    CustomCacheable annotation = getAnnotation(method, beanClass);  
                    if(annotation != null) {  
                        return cacheService.get(
	                        annotation.cacheName(), 
	                        constructKey(annotation, 
	                        invocation.getArguments()), 
	                        method.getGenericReturnType(),  
                                 () -> {  
                                     try {  
                                         return invocation.proceed();  
                                     } catch (
	                                     PaymentCardNotFoundException | 
	                                     AccessDeniedException e){ 
	                                     
                                          throw e;  
                                     }  
                                     catch (Throwable e) {  
                                         Logger log = LoggerFactory
	                                         .getLogger(beanClass);  
                                         log.error("Error during {} cacheable method execution", method.getName(), e);  
                                         throw new RuntimeException(e);  
                                     }  
                                 });  
                    } else  
                        return invocation.proceed();  
                }  
            );  
        return proxyFactory.getProxy(beanClass.getClassLoader());  
    }  
    return bean;  
}
```

> [!note]
> ProxyFactory - также интерфейс Spring AOP:
> `org.springframework.aop.framework.ProxyFactory`


----
#### [[MethodInterceptor - Spring AOP alliance - Flashcards|Link to flashcards]]



---
### References:

