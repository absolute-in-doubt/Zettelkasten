
10-01-2026 16:06

Status:

Tags: [[Spring]]

---
# Добавление статических ресурсов в WebApplicationContext - Spring

Добавление ресурсов может быть на двух уровнях:
- Уровне сервлет-контейнера (Tomact)
- Уровне [[WebApplicationContext - Spring MVC|webApplicationContext]]

#### Здесь расссматриваем добавление на уровне WebApplicationContext


---
## Добавляем через [[Конфигурация WebApplicationContext - Spring MVC|конфигурацию WebApplicationContext]]:

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry
            .addResourceHandler("/favicon.ico")
            .addResourceLocations("/favicon.ico"); // для webapp/favicon.ico

        // или если иконка лежит в /static в classpath:
        // registry.addResourceHandler("/favicon.ico")
        //         .addResourceLocations("classpath:/static/");
    }
}
```


---
## Как Spring ищет ресурсы

`addResourceLocations` принимает **строки‑локации** в одном из форматов:​

- `classpath:/...` — поиск внутри classpath (например, `src/main/resources/static` в Boot).
    
- `file:/abs/path/...` — абсолютный путь в файловой системе.
    
- `/что‑то/` (без префикса) — путь относительно **[[Структура war file|корня webapp]]** (только если приложение развернуто как «настоящее» веб‑приложение с `webapp`).


#### Пример:

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/resources/**")
            .addResourceLocations("/resources/");
}
```

Здесь `"/resources/"` — это как раз `webapp/resources/` внутри WAR/expanded‑WAR, а не classpath.


----
#### [[Добавление статических ресурсов в DispatcherServlet - Spring - Flashcards|Link to flashcards]]



---
### References:

