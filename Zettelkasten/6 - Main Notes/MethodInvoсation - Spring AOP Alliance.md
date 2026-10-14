
26-09-2026 12:13

Status:

Tags: [[Spring AOP]]

---
# MethodInvoсation - Spring AOP Alliance


Интерфейс непосредственно сввязанный с `MethodInvocation`:  мы принимаем его в качестве параметра.

```java
public interface MethodInterceptor extends Interceptor {

    Object invoke(MethodInvocation invocation)
        throws Throwable;
}
```


Он содержит информацию о текущем вызове:

```java
invocation.getMethod()
invocation.getArguments()
invocation.getThis()
invocation.proceed() 
```

`invocation.proceed()` - передаtn управление дальше по цепочке interceptor'ов, а в конце — реальному target-методу.

Например:

```
Interceptor A
     ↓ proceed()
Interceptor B
     ↓ proceed()
Target method
```


----
#### [[MethodInvokation - SSpring AOP Alliance - Flashcards|Link to flashcards]]



---
### References:

