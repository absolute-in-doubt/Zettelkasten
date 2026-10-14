
Theory for the cards: [[BeanPostProcessor proxifying vs Spring Aspects - Spring AOP]]

FILE TAGS:

Q: В чем различия Spring AOP Aspects и проксирования через BPP? В каких случаях что стоит использовать?
A: Spring AOP тоже создаёт proxy для вызова метода, просто часть кода выполняется под капотом. И это даёт меньше гибкости: нельзя полностью заменить bean например.
	 
Зачастую в AOP Aspects может выноситься cross-cutting buisness logic. В то время как BPP предназначены скорее для инфраструктуры. Но это все довольно условно.
	
Aspect может получить почти всю runtime информацию через JoinPoint / ProceedingJoinPoint.
	
Можно получить:
	
- method    
- args
- target bean
- proxy
- annotations
- return value
- exception
	
НЕ может:
	
- bean definition manipulation - так-то BPP тоже не может, нужен BFPP 
- lifecycle hooks
- replacing bean implementations
- low-level container infrastructure
	
```java
@Around("@annotation(Cacheable)")
public Object log(ProceedingJoinPoint pjp)
        throws Throwable {
	
    MethodSignature signature =
            (MethodSignature) pjp.getSignature();
	
    Method method = signature.getMethod();
	
    Object[] args = pjp.getArgs();
	
    Object target = pjp.getTarget();
	
    return pjp.proceed();
}
```
	
---
BPP имеет доступ к:
	
- original bean
- bean class
- bean name
- lifecycle stages
- **annotations before proxying**
<!--ID: 1790417057420-->
