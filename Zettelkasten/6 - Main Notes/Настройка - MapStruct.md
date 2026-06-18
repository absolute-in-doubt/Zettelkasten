
31-03-2026 19:00

Status:

Tags: [[MapStruct]] [[Java+]]

---
# Настройка - MapStruct


```xml
<properties>
    <org.mapstruct.version>1.5.5.Final</org.mapstruct.version>
</properties>
...
<dependencies>
	<dependency>
		<groupId>org.mapstruct</groupId>        
		<artifactId>mapstruct</artifactId>        
		<version>${org.mapstruct.version}</version>    
	</dependency>
</dependencies>
...
<build>
	<plugins>    
		<plugin>            
			<groupId>org.apache.maven.plugins</groupId>            
			<artifactId>maven-compiler-plugin</artifactId>            
			<version>3.8.1</version>            
			<configuration>                
				<source>1.8</source>                
				<target>1.8</target>                
				<annotationProcessorPaths>                    
					<path>                        
						<groupId>org.mapstruct</groupId>                       
						<artifactId>mapstruct-processor</artifactId>           
						<version>${org.mapstruct.version}</version>            
					</path>                
				</annotationProcessorPaths>            
			</configuration>        
		</plugin>    
	</plugins>
</build>
```



### Создание мапперов

1. **Объявляем интерфейс** (или абстрактный класс)
2. **Добавляем аннотацию ~={purple}@Mapper=~**
3. **Добавляем абстрактные методы** в наш интерфейс, реализацию которых возьмет на себя **MapStruct**

```java
@Mapper(componentModel = MappingConstants.ComponentModel.SPRING)  
public interface UserPreferencesDtoMapper {  
	  
    NotificationUserPreference toEntity(UserPreferencesDto dto);  
}
```


> [!warning] Note
> При указании `MappingConstants.ComponentModel.SPRING` созданный класс автоматически будет обозначен как **~={purple}@Component=~**


----
#### [[Настройка - MapStruct - Flashcards|Link to flashcards]]



---
### References:

