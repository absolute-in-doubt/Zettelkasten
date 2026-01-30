
10-01-2026 16:13

Status:

Tags: [[Spring]]

---
# Конфигурация WebApplicationContext - Spring MVC

Для конфигурации web контекста используется класс `WebConfigurer`, который содержит много предопределённых конфигураций бинов для web контекста.

Но никто не мешает просто поставить аннотацию ~={purple}@EnableWebMvc=~ (которая автоматически включается в SpringBoot).


Класс, помеченный `@Configuration` и `@EnableWebMvc` и реализующий `WebMvcConfigurer`, используется как точка расширения MVC‑настроек. Через его методы можно:​

- Добавить обработчики статических ресурсов (`addResourceHandlers`).

- Зарегистрировать view‑контроллеры (простые маппинги URL → view без логики) (`addViewControllers`).

- Настроить view‑резолверы (`configureViewResolvers`).

- Добавить форматтеры/конвертеры типов (`addFormatters`).

- Переопределить message converters (`configureMessageConverters`).

- Настроить CORS, обработку ошибок, content negotiation и т.д.​


Ключевая идея: **ты не переписываешь всю MVC‑конфигурацию**, а аккуратно дополняешь/кастомизируешь её.


```java
@Configuration  
@EnableWebMvc  
public class WebConfig implements WebMvcConfigurer {
```

### Примеры переопределений конфигураций бинов:

```java
@Override  
public void configureViewResolvers(ViewResolverRegistry registry){  
    ThymeleafViewResolver thymeleafViewResolver = new ThymeleafViewResolver();  
    thymeleafViewResolver.setTemplateEngine(templateEngine());  
    thymeleafViewResolver.setOrder(1);  
    registry.viewResolver(thymeleafViewResolver);  
}

@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
        // 1. Ресурсы из webapp/resources/
        registry.addResourceHandler("/resources/**")
                .addResourceLocations("/resources/");
                
        // 2. Ресурсы из classpath: /static и /public (например, для Boot)
        registry.addResourceHandler("/static/**")
                .addResourceLocations(
                        "classpath:/static/",
                        "classpath:/public/"
                );
                
        // 3. Отдаём favicon по корню
        registry.addResourceHandler("/favicon.ico")
                .addResourceLocations("/favicon.ico");
                
        // 4. Файлы с диска (вне приложения)
        registry.addResourceHandler("/files/**")
                .addResourceLocations("file:/opt/app/files/");
                
        // 5. Несколько URL‑шаблонов на один набор локаций
        registry.addResourceHandler("/img/**", "/images/**")
                .addResourceLocations("classpath:/images/");
    }
}

}

```





---
### References:

- [[Добавление статических ресурсов в WebApplicationContext - Spring]]
