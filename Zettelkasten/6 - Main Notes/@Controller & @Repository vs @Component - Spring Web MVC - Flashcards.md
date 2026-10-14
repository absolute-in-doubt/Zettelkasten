
Theory for the cards: [[@Controller & @Repository vs @Component - Spring Web MVC]]

FILE TAGS: java_interview spring

Q: Чем `@Controller` отличается от `@Component`?
A:   `@Controller` помимо регистрации в IoC контейнере, включает авто-регистрацию в DispatcherServlet и обработку `@RequestMapping`.
<!--ID: 1776865281267-->

Q: Расскажи что такое стериотипная аннотация в Spring и отдельно - можно ли создать свою стериотипную аннотацию? Какие для этого есть способы (обработки такой аннотации)?
A:   
- **Стереотипная аннотация** в Spring — аннотация, которая обозначает роль класса и позволяет Spring автоматически обнаружить/зарегистрировать его как bean. Основные примеры: `@Component`, `@Service`, `@Repository`, `@Controller`.
- **Свою создать можно.** Обычно используют `@Component` как мета-аннотацию: `@MyService` → `@Component`, а дополнительную логику реализуют через `BeanPostProcessor`/другие механизмы Spring.
- При этом **не обязательно** делать её мета-аннотацией `@Component`: можно самостоятельно зарегистрировать/обработать такие классы через `BeanDefinitionRegistryPostProcessor`, `ImportBeanDefinitionRegistrar` и т.п.
<!--ID: 1790346889616-->
