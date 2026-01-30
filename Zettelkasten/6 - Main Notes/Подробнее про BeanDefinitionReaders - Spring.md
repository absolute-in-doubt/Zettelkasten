
27-01-2026 15:51

Status: #baby

Tags:  [[Spring]]

---
# Подробнее про BeanDefinitionReaders - Spring

Все находятся в общем пакете `org.springframework.beans.factory`, для сокращения `<osbf>`

```
<osbf>.support.BeanDefinitionReader (interface)
 ├─ <osbf>.support.AbstractBeanDefinitionReader (abstract)
 │   ├─ <osbf>.support.PropertiesBeanDefinitionReader 
 │   └─ <osbf>.xml.XmlBeanDefinitionReader
 └─ <osbf>.groovy.GroovyBeanDefinitionReader

//======== НЕ реализуют BeanDefinitionsReader: ================

org.springframework.context.annotation.AnnotatedBeanDefinitionReader 

org.springframework.context.annotation.ConfigurationClassBeanDefinitionReader

```

> [!note]
> `PropertiesBeanDefinitionReader` - для конфигурации из `.properties` файла

> [!warning]
> Аннотационные `AnnotatedBeanDefinitionReader` и 
> `ConfigurationClassBeanDefinitionReader` – это отдельная линия, они не реализуют `BeanDefinitionReader`, а работают через `BeanDefinitionRegistry` и вызываются из инфраструктурного кода (`AnnotationConfigApplicationContext`, `ConfigurationClassPostProcessor`).



----
#### [[Подробнее про BeanDefinitionReaders - Spring - Flashcards|Link to flashcards]]



---
### References:

- [[Процесс инициализации ApplicationContext - Spring]]