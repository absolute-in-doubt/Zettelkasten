
19-05-2026 18:08

Status:

Tags: [[Spring Core]] [[Spring]]

---
# Создание Spring-aware proxy в BPP - Spring


Создание raw JDK Proxy:

```java
Proxy.newProxyInstance(...); //так делал Евгений Борисов
```

### Spring не знает о нем

Из-за этого:

- advisors не объединяются
- ordering ломается
- AOP infrastructure не контролирует proxy
- annotation scanning может ломаться


Теряется Spring proxy chain integration

- Spring ожидает `Advised proxy`

- А не обычный `JDK proxy`


---
### Правильный способ

Использовать `ProxyFactory` или `MethodInterceptor`.

Тогда Spring сможет

- добавлять новые advices
- объединять interceptors
- сохранять proxy metadata
- корректно работать с AOP


### Пример:

```java
@Override  
public @Nullable Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {  
  
    Class<?> beanClass = map.get(beanName);  
    if(beanClass != null){  
        ProxyFactory proxyFactory = new ProxyFactory(bean);  
        proxyFactory.addAdvice( (MethodInterceptor) invocation -> {  
                    Method method = invocation.getMethod();  
                    CustomCacheable annotation = getAnnotation(method, beanClass);  
                    if(annotation != null) {  
                        return cacheService.get(annotation.cacheName(), constructKey(annotation, invocation.getArguments()), method.getGenericReturnType(),  
                                 () -> {  
                                     try {  
                                         return invocation.proceed();  
                                     } catch (Throwable e) {  
                                         Logger log = LoggerFactory.getLogger(beanClass);  
                                         log.error("Error during {} cacheable method execution", method.getName(), e);  
                                         throw new RuntimeException(e);  
                                     }  
                                 });  
                    } else  
                        return invokation.proceed();  
                }  
            );  
        return proxyFactory.getProxy(bean.getClass().getClassLoader());  
    }  
    return bean;  
}  
  
private CustomCacheable getAnnotation(Method method, Class<?> beanClass){  
    CustomCacheable annotation = method.getAnnotation(CustomCacheable.class);  
    if(annotation != null)  
        return annotation;  
    method = AopUtils.getMostSpecificMethod(method, beanClass);  
    return method.getAnnotation(CustomCacheable.class);  
}
```

> [!warning]
> Вместо `method.invoke()` используем метод `proceed` объекта `MethodInterceptor`, таким образом, метод вызовется не сразу же. Кусок логики вокруг метода сохранится и когда Spring определит оптимальный порядок проксирования, он будет вызван.



---
`MethodInterceptor` обычно получает `Method исходного target interface/class`, а не method generated proxy class.

~={red}**То есть обычно достаточно использовать просто `.getMethod()` объекта `MethodInterceptor` чтобы получить исходный метод и проверить там аннотации**=~

### Но есть нюанс

Для:

- JDK proxies
- multiple proxies
- bridge methods
- interface methods

annotation действительно может потеряться.

---

# Поэтому production-подход:

никогда не полагаться напрямую на:

```
invocation.getMethod()
```

---

# Вместо этого

нужно получать:

```
most specific target method
```


---
### Проверка аннотаций с использованием `AOPUtils`

```java
private CustomCacheable getAnnotation(Method method, Class<?> beanClass){  
    CustomCacheable annotation = method.getAnnotation(CustomCacheable.class);  
    if(annotation != null)  
        return annotation;  
    method = AopUtils.getMostSpecificMethod(method, beanClass);  
    return method.getAnnotation(CustomCacheable.class);  
}
```


----
#### [[Создание Spring-aware proxy в BPP - Spring - Flashcards|Link to flashcards]]



---
### References:

