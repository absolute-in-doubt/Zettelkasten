
Index: [[Spring Data MongoDB]] [[Mongo DB]]


```xml
<dependencyManagement>    
	<dependencies>        
		<dependency>            
			<groupId>io.mongock</groupId>            
			<artifactId>mongock-bom</artifactId>            
			<version>5.5.0</version>            
			<type>pom</type>
			<scope>import</scope>        
		</dependency>
	</dependencies>
</dependencyManagement>

<dependency>
    <groupId>io.mongock</groupId>
    <artifactId>mongock-springboot</artifactId>
</dependency>

<dependency>
    <groupId>io.mongock</groupId>
    <artifactId>mongodb-springdata-v3-driver</artifactId>
</dependency>
```

- [[Создание скрипта миграции - Mongock]]



----
https://docs.mongock.io/v5/get-started/