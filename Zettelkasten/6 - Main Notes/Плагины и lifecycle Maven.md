
03-01-2026 16:11

Status:

Tags:

---
# Плагины и lifecycle Maven

Плагины — это «исполнители» конкретных задач, а lifecycle — это только **набор фаз с именами**.​

Важно три идеи:

1. **Lifecycle сам по себе ничего не делает.**
    
    - Фазы `compile`, `test`, `package`, `install`, `deploy` — это просто «крючки».
        
    - Конкретную работу (компиляция, тесты, упаковка в JAR/WAR, заливка в репозиторий) выполняют **goals плагинов**, привязанные к этим фазам​
        
2. **Плагины навешиваются на фазы либо по умолчанию, либо через pom.xml.**
    
    - Пример: `maven-compiler-plugin` по умолчанию связан с фазой `compile` и `test-compile`, `maven-surefire-plugin` — с `test`, `maven-jar-plugin` — с `package` для packaging=jar.​
        
    - В pom.xml ты можешь переопределить версию плагина, добавить конфигурацию и ~={cyan}при необходимости привязать новые goals к нужным фазам=~. Это и есть способ **расширять или изменять поведение жизненного цикла** под нужды проекта.​
        
3. **Так плагины «управляют жизненным циклом ПО»**:
    
    - На разных стадиях CI/CD вызываются те же самые фазы (`test`, `verify`, `deploy`) — а значит, и соответствующие goals плагинов: тестирование, сборка отчётов, статический анализ (SpotBugs, Checkstyle), упаковка, публикация артефактов и т.д.​
        
    - Меняя набор/конфигурацию плагинов в pom.xml, ты фактически меняешь, что именно происходит на каждой стадии lifecycle, не меняя саму структуру lifecycle.



```xml
<?xml version="1.0" encoding="UTF-8"?> <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"> 
	<modelVersion>4.0.0</modelVersion> 
	<groupId>example.com</groupId> 
	<artifactId>example</artifactId> 
	<version>1.0-SNAPSHOT</version> 
	
	<build> 
		<plugins> 
			<plugin> 
				<groupId>com.soebes.maven.plugins</groupId> 
				<artifactId>uptodate-maven-plugin</artifactId> 
				<version>0.2.0</version> 
				<executions> 
					<execution> 
						<goals> 
							<goal>dependency</goal> 
						</goals> 
						<phase>validate</phase> 
					</execution> 
				</executions> 
			</plugin> 
		</plugins> 
	</build> 
	
	<dependencies> 
		<dependency> 
			<groupId>commons-io</groupId> 
			<artifactId>commons-io</artifactId> 
			<version>2.6</version> 
		</dependency>
	</dependencies> 
</project>
```


----
#### [[Плагины и lifecycle Maven - Flashcards|Link to flashcards]]



---
### References:

