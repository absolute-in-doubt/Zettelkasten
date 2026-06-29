
20-06-2026 10:25

Status:

Tags: [[Spring Core]] [[Spring]]

---
# i18n - ApplicationContext - Spring

Определяется интерфейсом `MessageSource`. 

```java
public interface MessageSource { 
	 
	// 1. Без default — бросает NoSuchMessageException, если ключ не найден
    @Nullable String getMessage(
		    String code, 
		    Object @Nullable [] args, 
		    @Nullable String defaultMessage, 
		    @Nullable Locale locale
		);  
		
	// 2. С default — возвращает default, если ключ не найден
    String getMessage(
		    String code, 
		    Object @Nullable [] args, 
		    @Nullable Locale locale
		) throws NoSuchMessageException;  
		  
	// 3. Через MessageSourceResolvable
    String getMessage(
		    MessageSourceResolvable resolvable, 
		    @Nullable Locale locale
		) throws NoSuchMessageException;  
}
```

[[ApplicationContext - Spring#5. MessageSource|ApplicationContext]] реализует данный интерфейс.


----
### Как работает `getMessage()` под капотом


## Пошаговый процесс:

| Шаг   | Что происходит                                                                                             |
| ----- | ---------------------------------------------------------------------------------------------------------- |
| **1** | `ApplicationContext` ищет bean с id `messageSource` в контексте                                            |
| **2** | Если не находит — проверяет родительский контекст                                                          |
| **3** | Если не находит вовсе — создает пустой `StaticMessageSource`                                               |
| **4** | Вызов делегируется найденному `MessageSource` (обычно `ResourceBundleMessageSource`)                       |
| **5** | `ResourceBundleMessageSource` использует стандартный JDK `ResourceBundle` для загрузки `.properties` файла |
| **6** | Если ключ найден — параметры `args` заменяются через `MessageFormat` (заглушки `{0}`, `{1}`)               |
| **7** | Если ключ не найден — без default: `NoSuchMessageException`, с default: возвращается default-строка        |

> [!warning] Note
> 
>ApplicationContext **реализует** MessageSource, но **НЕ использует себя напрямую**
>
>```java
>// ApplicationContext интерфейс расширяет MessageSource
>public interface ApplicationContext extends EnvironmentCapable, 
>    ListableBeanFactory, ResourceLoader, MessageSource, DisposableBean {
>```
> Но делегирование работает через bean с id `messageSource`
>
> Когда ты вызываешь `context.getMessage("hello", null, locale)`:
>
>1. `AbstractApplicationContext` (базовый класс) имеет метод `getMessage()` из интерфейса `MessageSource`.
>2. Этот метод **не реализован в самом ApplicationContext**, а делегируется на внутренний bean.
>3. Spring **ищет bean с id `messageSource`** в контексте.
>4. Если нашел — **делегитирует** все вызовы `getMessage()` на этот bean.
>5. Если не нашел — проверяет **родительский контекст**.
>6. Если совсем не нашел — создает пустой `DelegatingMessageSource` (fallback).
>
>Инициализация `MessageSource` происходит в `initMessageSource()`:
>```java
>// AbstractApplicationContext.java
>protected void initMessageSource() {
>    ConfigurableListableBeanFactory beanFactory = getBeanFactory();
 >   
 >   // Ищем bean с именем "messageSource"
 >   if (beanFactory.containsLocalBean(MESSAGE_SOURCE_BEAN_NAME)) {
 >       // ДЕЛЕГИРУЕМ на найденный bean
>       this.messageSource = beanFactory.getBean(MESSAGE_SOURCE_BEAN_NAME, MessageSource.class);
>    } else {
>        // Создаем пустой fallback
>        this.messageSource = new DelegatingMessageSource(getParentMessageSource());
>    }
>}
>```


**~={red}?!=~** **ApplicationContext реализует интерфейс MessageSource МЕТОДАМИ, но внутреннюю реализацию ДЕЛЕГИРУЕТ на bean с id `messageSource`**

```java
// Без конфигурации messageSource bean:
ApplicationContext context = new ClassPathXmlApplicationContext("config.xml");
// Весернет empty DelegatingMessageSource — getMessage() будет работать, но не найдёт сообщений

// С конфигурацией:
@Bean
public MessageSource messageSource() {
    return new ResourceBundleMessageSource();
}
// Теперь context.getMessage() делегируется на этот bean
```



## Пример работы с параметрами:

```java
// ResourceBundle: argument.required = The '{0}' argument is required.
String message = context.getMessage("argument.required", 
    new Object[]{"userDao"}, 
    "Required", 
    null);
// Результат: "The 'userDao' argument is required."
```

Параметры `{0}` заменяются через `java.text.MessageFormat`.

---

## Как правильно реализовать хранение сообщений

#### 1. Структура файлов (рекомендуемая):

```
resources/
  i18n/
    messages.properties          # Default (например, en)
    messages_ru.properties       # Русский
    messages_zh_CN.properties    # Китайский
    errors.properties            # Ошибки (default)
    errors_ru.properties
```

#### 2. Конфигурация `MessageSource` bean:

```java
@Configuration
public class I18nConfig {
    
    @Bean
    public MessageSource messageSource() {
        ReloadableResourceBundleMessageSource messageSource = 
            new ReloadableResourceBundleMessageSource();
        
        messageSource.setBasename("classpath:i18n/messages");
        messageSource.setDefaultEncoding("UTF-8");
        messageSource.setCacheSeconds(10); // Перезагрузка каждые 10 сек
        messageSource.setFallbackToSystemLocale(true);
        
        return messageSource;
    }
}
```

Для Spring Boot можно в `application.yml`:

```yaml
spring:
  messages:
    basename: i18n/messages
    default-encoding: UTF-8
    fallback-to-system-locale: true
```

#### 3. Формат `.properties` файлов:

```properties
# messages.properties (en)
hello.text=Hello, {0}!
user.created=User {0} was created at {1}
greeting=Welcome to our application

# messages_ru.properties
hello.text=Привет, {0}!
user.created=Пользователь {0} был создан в {1}
greeting=Добро пожаловать в наше приложение
```

#### 4. Использование в коде:

```java
// В Controller/Service
@Autowired
private MessageSource messageSource;

@GetMapping("/user/{id}")
public ResponseEntity<String> getUser(@PathVariable Long id, 
                                      Locale locale) {
    String message = messageSource.getMessage(
        "hello.text", 
        new Object[]{id}, 
        locale
    );
    return ResponseEntity.ok(message);
}
```

#### 5. Автоматическое определение Locale (для веб-приложений):

```java
// В конфигурации Spring MVC
@Bean
public LocaleResolver localeResolver() {
    SessionLocaleResolver resolver = new SessionLocaleResolver();
    resolver.setDefaultLocale(Locale.ENGLISH);
    return resolver;
}

@Bean
public LocaleChangeInterceptor localeChangeInterceptor() {
    LocaleChangeInterceptor interceptor = new LocaleChangeInterceptor();
    interceptor.setParamName("lang"); // ?lang=ru
    return interceptor;
}
```


---
## В каких случаях i18n оправдано

#### ✅ Оправдано:

| Сценарий                                                 | Почему                                            |
| -------------------------------------------------------- | ------------------------------------------------- |
| **Публичные веб-приложения** для международной аудитории | Пользователи из разных стран требуют разные языки |
| **REST API** с клиентами на разных языках                | Ответы API (ошибки, сообщения) на языке клиента   |
| **Email-уведомления** для глобальных пользователей       | Уведомления на языке пользователя                 |
| **UI приложений** (Thymeleaf, Android, desktop)          | Интерфейс на_native_ языке                        |
| **Логирование с параметрами** для мультиязычных команд   | Логи понятны разработчикам из разных стран        |

## ❌ Не оправдано:

|Сценарий|Почему|
|---|---|
|**Внутренние backend-системы** с одной локалью|Лишняя сложность, нет пользы|
|**Простые CLI-скрипты**|Проще вывести текст напрямую|
|**База данных с данными на одном языке**|Содержимое БД не меняется по локали|
|**Микросервисы без прямого доступа пользователей**|Сообщения только для разработчиков одной команды|

## Комплексный подход для Spring Boot (Пример):

```java
// Утилита для удобного получения сообщений
public class MessageUtils {
    
    @Autowired
    private static MessageSource messageSource;
    
    public static String getMessage(String code, Object... args) {
        Locale locale = LocaleContextHolder.getLocale(); // Из текущей сессии
        return messageSource.getMessage(code, args, locale);
    }
    
    public static String getMessage(String code, Locale locale, Object... args) {
        return messageSource.getMessage(code, args, locale);
    }
}
```


----
#### [[i18n - ApplicationContext - Spring - Flashcards|Link to flashcards]]



---
### References:

