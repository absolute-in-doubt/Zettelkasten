
04-06-2026 10:55

Status:

Tags: [[Spring Core]] [[Spring]]

---
# How to inject prototype into singleton - Spring Core


Когда нужно внедрить **prototype-бин в singleton-бин**, возникает проблема:

```java
@Service
public class SingletonService {

    @Autowired
    private PrototypeBean prototypeBean;
}
```

```java
@Component
@Scope("prototype")
public class PrototypeBean {
}
```

Несмотря на `prototype`, экземпляр будет создан **один раз во время создания singleton-а**, и далее использоваться повторно.

Для получения нового prototype при каждом вызове есть несколько способов.


---
# 1. `ObjectProvider` (обычно лучший вариант)

Самый современный и наиболее используемый способ.

```java
@Service
@RequiredArgsConstructor
public class SingletonService {

    private final ObjectProvider<PrototypeBean> provider;

    public void process() {
        PrototypeBean bean = provider.getObject();
    }
}
```

Каждый вызов:

```java
provider.getObject()
```

создает новый prototype.

---

### Под капотом

Spring внедряет не сам объект:

```java
PrototypeBean
```

а фабрику:

```java
ObjectProvider<PrototypeBean>
```

которая обращается к `BeanFactory`:

```java
beanFactory.getBean(PrototypeBean.class)
```

при каждом вызове.

---

### Плюсы

- Явный код.
    
- Нет магии.
    
- Легко тестировать.
    
- Поддерживает optional-бин:
    

```java
provider.getIfAvailable();
```

---

# 2. `@Lookup`

Позволяет объявить абстрактный метод.

```java
@Service
public abstract class SingletonService {

    public void process() {
        PrototypeBean bean = getPrototypeBean();
    }

    @Lookup
    protected abstract PrototypeBean getPrototypeBean();
}
```

---

### Что делает Spring

Во время создания бина Spring генерирует CGLIB-подкласс:

```java
SingletonService$$SpringCGLIB
```

и переопределяет метод:

```java
protected PrototypeBean getPrototypeBean() {
    return beanFactory.getBean(PrototypeBean.class);
}
```

---

### Плюсы

Код выглядит красиво:

```java
PrototypeBean bean = getPrototypeBean();
```

---

### Минусы

Много магии.

Неочевидно для разработчика:

```java
getPrototypeBean();
```

на самом деле делает вызов контейнера.

Также:

- не работает с final-классами;
    
- не работает с final-методами;
    
- использует CGLIB.
    

---

# 3. Scoped Proxy (`proxyMode = TARGET_CLASS`)

Можно заставить Spring внедрить прокси вместо prototype.

```java
@Component
@Scope(
    value = ConfigurableBeanFactory.SCOPE_PROTOTYPE,
    proxyMode = ScopedProxyMode.TARGET_CLASS
)
public class PrototypeBean {
}
```

---

Теперь:

```java
@Service
@RequiredArgsConstructor
public class SingletonService {

    private final PrototypeBean prototypeBean;
}
```

будет содержать не prototype, а прокси:

```java
PrototypeBean$$SpringCGLIBProxy
```

---

При каждом вызове метода:

```java
prototypeBean.doWork();
```

прокси делает:

```java
beanFactory.getBean(PrototypeBean.class)
```

и делегирует вызов новому экземпляру.

---

### Важно

Работает так:

```java
prototypeBean.doWork();  // новый prototype
prototypeBean.doWork();  // еще один prototype
prototypeBean.doWork();  // еще один prototype
```

Каждый вызов метода может работать с разным объектом.

---

### Подводный камень

Такой код:

```java
PrototypeBean bean = prototypeBean;
```

не создает новый prototype.

Новый объект появляется только при вызове метода через прокси.

---

# 4. Инъекция `BeanFactory`

Самый низкоуровневый вариант.

```java
@Service
@RequiredArgsConstructor
public class SingletonService {

    private final BeanFactory beanFactory;

    public void process() {
        PrototypeBean bean =
            beanFactory.getBean(PrototypeBean.class);
    }
}
```

---

Это фактически то, что делают предыдущие варианты под капотом.

Обычно считается менее красивым, потому что сервис начинает зависеть от контейнера Spring напрямую.

---

# Сравнение

|Способ|Как работает|Плюсы|Минусы|
|---|---|---|---|
|`ObjectProvider`|Явный вызов `getObject()`|Прозрачно, удобно тестировать|Нужно явно вызывать|
|`@Lookup`|CGLIB переопределяет метод|Красивый API|Магия, ограничения CGLIB|
|`Scoped Proxy`|Внедряется прокси|Полностью прозрачно для клиента|Неочевидное поведение|
|`BeanFactory`|Ручной `getBean()`|Максимальный контроль|Service Locator anti-pattern|

---

# Что используют на практике?

В большинстве современных Spring Boot проектов предпочтение обычно отдают:

```java
ObjectProvider<PrototypeBean>
```

или реже

```java
Provider<PrototypeBean>   // jakarta.inject.Provider
```

потому что это самый явный и понятный способ.

`@Lookup` встречается редко, а `ScopedProxyMode.TARGET_CLASS` чаще используют для веб-скоупов (`request`, `session`), чем для обычных prototype-бинов.


----
#### [[How to inject prototype into singleton - Spring Core - Flashcards|Link to flashcards]]



---
### References:

