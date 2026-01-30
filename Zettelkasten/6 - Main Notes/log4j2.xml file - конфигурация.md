
08-01-2026 10:26

Status:

Tags: [[Log4j2]] [[Logging]] [[Java+]]

---
# log4j2.xml file - конфигурация


Простейший пример с выводом в консоль:

```xml

<?xml version="1.0" encoding="UTF-8"?> <Configuration xmlns="https://logging.apache.org/xml/ns" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation=" https://logging.apache.org/xml/ns https://logging.apache.org/xml/ns/log4j-config-2.xsd">
    <Appenders>
        <Console name="console_appender" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} %p %m%n"/>
        </Console>
        
		<File name="fout" fileName="baeldung.log" append="true"> 
			<PatternLayout> 
				<Pattern>%d{yyyy-MM-dd HH:mm:ss} %-5p %m%n</Pattern> 
			</PatternLayout> 
		</File>
    </Appenders>
    
    
    <!-- Overrides log level for specified package -->
    <Logger name="com.baeldung.log4j2" level="debug">
	    <AppenderRef ref="stdout"/>
	</Logger>
    
    
    
    <Loggers> 
	    <Root>
		    <level value="ERROR" /> <!-- Устанавливает базовый уровень логгирования ERROR (все сообщения ниже ютого уровня не отображаются)-->
	        <AppenderRef ref="console_appender"/>
	        <AppenderRef ref="fout"/>
	    </Root>
	</Loggers>
</Configuration>
```

`PatternLayout` - определяет, как лог будет отформатирован. 
_%d_  – determines the date pattern, 
_%p_ – the log level output, 
_%m_ – output of the logged message,
_%n_ – adds a new line symbol.


### Для асинхронного логгирования

Используем `AsyncRoot`:

```xml
<AsyncRoot level="DEBUG">
    <AppenderRef ref="console_appender" />
    <AppenderRef ref="fout"/>
</AsyncRoot>
```



----
#### [[log4j2.xml file - конфигурация - Flashcards|Link to flashcards]]



---
### References:

