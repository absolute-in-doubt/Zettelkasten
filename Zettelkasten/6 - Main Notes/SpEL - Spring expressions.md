
20-05-2026 13:14

Status:

Tags:

---
# SpEL (Spring Expression Language)

SpEL — expression language из Spring Framework.

Используется в:

- `@Cacheable`
    
- `@Value`
    
- `@PreAuthorize`
    
- `@Scheduled`
    
- Spring Integration
    
- Spring Security


---
# Основной модуль

```text
spring-expression
```

---

# Главные классы

|Класс|Назначение|
|---|---|
|`ExpressionParser`|парсинг expression|
|`SpelExpressionParser`|основная реализация parser|
|`Expression`|скомпилированное expression|
|`EvaluationContext`|context с variables/functions/root object|
|`StandardEvaluationContext`|стандартная реализация context|
|`SpelExpression`|runtime representation expression|

---

# Как работает SpEL

---

## 1. Expression парсится

```java
ExpressionParser parser =
        new SpelExpressionParser();

Expression expression =
        parser.parseExpression(
                "#user.id"
        );
```

---

## 2. Создается context

```java
StandardEvaluationContext context =
        new StandardEvaluationContext();
```

---

## 3. В context кладутся variables

```java
context.setVariable("user", user);
```

---

## 4. Expression вычисляется

```java
Object result =
        expression.getValue(context);
```

---

# Пример

```java
ExpressionParser parser =
        new SpelExpressionParser();

Expression expression =
        parser.parseExpression(
                "#a0 + ':' + #a1"
        );

StandardEvaluationContext context =
        new StandardEvaluationContext();

context.setVariable("a0", 10);
context.setVariable("a1", "abc");

String key =
        expression.getValue(
                context,
                String.class
        );
```

---

# Как это использует Spring Cache

Например:

```java
@Cacheable(key = "#id")
```

---

Spring:

- получает Method
    
- получает args
    
- создает `EvaluationContext`
    
- кладет туда args
    
- вычисляет expression
    

---

# Какие variables обычно доступны

|Variable|Значение|
|---|---|
|`#root`|root object|
|`#args`|arguments array|
|`#a0`|первый аргумент|
|`#p0`|первый аргумент|
|`#result`|result method invocation|

---

# Как использовать в custom annotation

---

## Annotation

```java
public @interface CustomCacheable {

    String key() default "";
}
```

---

## Parsing

```java
ExpressionParser parser =
        new SpelExpressionParser();

Expression expression =
        parser.parseExpression(
                annotation.key()
        );
```

---

## Context

```java
StandardEvaluationContext context =
        new StandardEvaluationContext();

context.setVariable("a0", args[0]);
context.setVariable("a1", args[1]);
```

---

## Resolve

```java
String key =
        expression.getValue(
                context,
                String.class
        );
```

---

# Важная проблема

`parseExpression()`

дорогой.

---

# Поэтому Spring

кэширует parsed expressions.

---

# Обычно делают

```java
Map<String, Expression> cache =
        new ConcurrentHashMap<>();
```

---

# И потом

```java
Expression expression =
        cache.computeIfAbsent(
                spel,
                parser::parseExpression
        );
```

---

# Production-grade классы Spring Cache

|Класс|Назначение|
|---|---|
|`CacheOperationExpressionEvaluator`|evaluation|
|`CachedExpressionEvaluator`|expression cache|
|`MethodBasedEvaluationContext`|context для method args|

---

# Самые важные классы

```text
SpelExpressionParser
Expression
StandardEvaluationContext
```

---

# Минимальный production-style набор

```java
private final ExpressionParser parser =
        new SpelExpressionParser();

private final Map<String, Expression> cache =
        new ConcurrentHashMap<>();
```


----
#### [[SpEL - Spring expressions - Flashcards|Link to flashcards]]



---
### References:

