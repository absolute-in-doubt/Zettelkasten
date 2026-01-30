
Theory for the cards: [[Configuration via Annotations - Spring]]

FILE TAGS: spring spring_core

Q: Что делает аннотация @Value? Есть ли какие-то доп. зависимости, чтобы она работала?
A: @Value нужна для инъекции констант и значений из `.properties`:
	
> [!warning]
> `PropertyPlaceholderConfigurer` нужно самому добавлять в контекст:
>
> В XML:
> ```xml
> <!-- Обязательно! --> <context:property-placeholder location="classpath:app.properties"/>
> ```
>
> В Java-config:
> ```java
@Configuration
public class AppConfig {
>    
>    @Bean
>    public static PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer() {
>        PropertySourcesPlaceholderConfigurer configurer = new PropertySourcesPlaceholderConfigurer();
>        configurer.setLocation(new ClassPathResource("app.properties"));
>        configurer.setIgnoreUnresolvablePlaceholders(true);
>        return configurer;
>    }
>    
>    @Bean
>    @Value("${db.url}")
>    public DataSource dataSource() {
>        // ${db.url} работает!
>    }
>}
> ```
<!--ID: 1769522954787-->

Q: Как установить scope для бина в Java config?
A: С помощью аннотации @Scope:
	
![[Pasted image 20251018170053.png]]
<!--ID: 1769523029073-->

Q: Как избежать ошибки NonUniqueBeanException при внедрении зависимостей?
A: Использовать аннотацию @Qualifier:
- на поле:
![[Pasted image 20251018161100.png]]
	
- на параметре метода:
![[Pasted image 20251018161137.png]]
<!--ID: 1769523180389-->

