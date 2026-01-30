
08-01-2026 11:01

Status:
 
Tags: [[Log4j2]] [[Logging]] [[Java+]]

---
# log4j2.properties file - конфигурация

Альтернатива конфигурации xml. Не требует дополнительных зависимостей.

### Правила

Конфигурация строится по принципу дерева. Каждая node - отдельный плагин (`Appender`, `Logger`, `Layout`). 


1. Элементы с одинаковым префиксом (например `appender.foo`) мапятся в поддерево (`node`) конфигурационного дерева. 

2. Атрибуты ноды указываются используя постфикс названия настройки (имени поля): `name` для `appender.foo.name`

3. тип плагина (ноды) должен быть указан с именем поля заканчиващимся на `type`

4. Вложенные элементы создаются путем:
	- Выбираем любой id (например `<0>`)
	- Добавляем этот id после префикса родителя (например `appender.foo.<0>`)
	- Обязательно указываем тип child ноды: `appender.foo.<0>.type`


### Пример конфигурации

```properties
appender.0.type = Console
appender.0.name = CONSOLE
appender.0.layout.type = PatternLayout
appender.0.layout.pattern = %p - %m%n

appender.1.type = File
appender.1.name = MAIN
appender.1.fileName = logs/main.log
appender.1.layout.type = JsonTemplateLayout

appender.2.type = File
appender.2.name = DEBUG_LOG
appender.2.fileName = logs/debug.log
appender.2.layout.type = PatternLayout
appender.2.layout.pattern = %d [%t] %p %c - %m%n

rootLogger.level = INFO
rootLogger.appenderRef.0.ref = CONSOLE
rootLogger.appenderRef.0.level = WARN
rootLogger.appenderRef.1.ref = MAIN

logger.0.name = org.example         <= специальный логгер для конкретного пакета
logger.0.level = DEBUG
logger.0.appenderRef.0.ref = DEBUG_LOG

logger.1.name=org.example.MyClass   <= специальный логгер для конкретного класса
logger.1.level=DEBUG 
logger.1.appenderRef.0.ref=console 
logger.1.additivity=false   <= отключаем аддитивность, чтобы root логгер не обрабатывал сообщения обработанные данным логгером
```

> [!tip]
> Log4j2 строит иерархию не по структуре классов, а по **строке имени логгера**, разделённой точками.


> [!note]
> Если для конкретного имени логгера (например, `org.example.service.UserService`) отдельный `logger.*` не задан, он наследует уровень, appender’ы и прочие свойства из ближайшего родителя в иерархии (`org.example`, затем `org`, затем `root`).

Таким образом, здесь логирование будет выполнятся следующим образом:

| Logger name           | Log event level | Appenders                      |
| --------------------- | --------------- | ------------------------------ |
| `org.example.foo`     | `WARN`          | `CONSOLE`, `MAIN`, `DEBUG_LOG` |
| `org.example.foo`     | `DEBUG`         | `MAIN`, `DEBUG_LOG`            |
| `org.example.foo`     | `TRACE`         | _none_                         |
| `com.example`         | `WARN`          | `CONSOLE`, `MAIN`              |
| `com.example`         | `INFO`          | `MAIN`                         |
| `com.example`         | `DEBUG`         | _none_                         |
| `org.example.MyClass` | `DEBUG`         | `CONSOLE`                      |



----
#### [[log4j2.properties file - конфигурация - Flashcards|Link to flashcards]]



---
### References:

