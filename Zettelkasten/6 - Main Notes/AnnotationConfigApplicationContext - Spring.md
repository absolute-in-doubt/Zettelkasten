
24-01-2026 10:14

Status:

Tags: [[Spring]]

---
# AnnotationConfigApplicationContext - Spring

В контексте _AnnotationConfigApplicationContext_ есть 2 приватных поля:  
  

- _private final AnnotatedBeanDefinitionReader reader_ (работает с JavaConfig);
- _private final ClassPathBeanDefinitionScanner scanner_ (сканирует пакет).


Особенность **AnnotatedBeanDefinitionReader** в том, что он работает в несколько этапов:  
  
1. Регистрация всех _@Configuration_-файлов для дальнейшего парсинга;

2. Регистрация специального **BeanFactoryPostProcessor**, а именно **BeanDefinitionRegistryPostProcessor**, который при помощи класса **ConfigurationClassParser** парсит JavaConfig и создает **BeanDefinition**.


----
#### [[AnnotationConfigApplicationContext - Spring - Flashcards|Link to flashcards]]



---
### References:

