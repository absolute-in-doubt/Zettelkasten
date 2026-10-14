
07-10-2026 13:05

Status:

Tags: [[JOOQ]] [[Java+]]

---
# JOOQ Generator

При использовании `jooq-meta-extensions-liquibase`, генератор jOOQ во время фазы `generate-sources` поднимает виртуальную базу данных в памяти (обычно H2 или SQLite) и прогоняет через неё ваши YAML/XML-миграции из `src/main/resources/db/changelog/...`, чтобы прочитать структуру таблиц.


```xml
<plugin>  
   <groupId>org.jooq</groupId>  
   <artifactId>jooq-codegen-maven</artifactId>  
   <version>${jooq.version}</version>  
   <executions>          
	   <execution>             
			<id>jooq-codegen</id>  
			<phase>generate-sources</phase>  
			<goals>                
				<goal>generate</goal>  
			</goals>          
		</execution>       
	</executions>       
	<dependencies>          
		<dependency>             
			<groupId>org.jooq</groupId>  
			<artifactId>jooq-meta-extensions-liquibase</artifactId>  
			<version>${jooq.version}</version>  
		</dependency>       
	</dependencies>       
	<configuration>          
		<generator>             
			<database>               
			<name>org.jooq.meta.extensions.liquibase.LiquibaseDatabase</name>  
			<properties>
				<property>                      
					<key>rootPath</key>  
					<value>${project.basedir}/src/main/resources</value>  
				</property>                   
				<property>                      
					<key>scripts</key>  
					<value>db/changelog/db.changelog-master.yaml</value>  
				</property>                   
				<property>                      
					<key>includeLiquibaseTables</key>  
					<value>false</value>  
				</property>                
			</properties>             
			</database>             
			<target>                
		<packageName>com.example.spending.service.infrastructure.jooq</packageName>  
				<directory>target/generated-sources/jooq</directory>  
			</target>          
		</generator>       
	</configuration>    
</plugin>    
<plugin>       
	<groupId>org.codehaus.mojo</groupId>  
	<artifactId>build-helper-maven-plugin</artifactId>  
	<executions>          
		<execution>             
			 <id>add-jooq-sources</id>  
			<phase>generate-sources</phase>  
			<goals>                
				<goal>add-source</goal>  
			</goals>             
			<configuration>                
				<sources>                   
					<source>target/generated-sources/jooq</source>  
				</sources>             
			</configuration>          
		</execution>       
	</executions>    
</plugin>
```

Используется в связке со Spring Liquibase.


----
#### [[JOOQ Generator - Flashcards|Link to flashcards]]



---
### References:

