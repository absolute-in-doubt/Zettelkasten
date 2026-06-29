
28-06-2026 10:45

Status:

Tags: [[Mongock]] [[Spring Data MongoDB]] [[Mongo DB]]

---
# Создание скрипта миграции - Mongock



```java
package io.mongock.examples.migration;
import io.mongock.api.annotations.Execution;
import io.mongock.api.annotations.ChangeUnit;
import io.mongock.api.annotations.RollbackExecution;

@ChangeUnit(
	id="client-initializer", 
	order = "001", 
	author = "mongock"
)
public class ClientInitializerChange {  
	private final MongoTemplate mongoTemplate;  
	private final ThirPartyService thirdPartyService;  
	
	
	public ClientInitializerChange(
			MongoTemplate mongoTemplate, 
			ThirPartyService thirdPartyService
		) {    
		
		this.mongoTemplate = mongoTemplate;
		this.thirdPartyService = thirdPartyService;  
	}  
	
	
	/** This is the method with the migration code **/  
	@Execution  
	public void changeSet() {    
	
		thirdPartyService.getData()            
			.stream()            
			.forEach(client -> mongoTemplate.save(client, CLIENTS_COLLECTION_NAME));  
			
	}  
	
	
	/**   
	This method is mandatory even when transactions are enabled.   
	They are used in the undo operation and any other scenario where transactions are not an option.   
	However, note that when transactions are avialble and Mongock need to rollback, this method is ignored.   
	**/ 
	 @RollbackExecution  
	 public void rollback() {    
		 mongoTemplate.deleteMany(new Document());  
		 }
	 }
```



### Регистрируем скрипт миграции (для Spring Data + Boot autoconfig)

```yaml
mongock:  
	migration-scan-package:    
		- io.mongock.examples.migration
```


---
### Для чистого Spring

Нужно еще дополнительно добавить ~={purple}**@EnableMongock**=~.

However, this approach requires the Spring ApplicationContext and MongoTemplate to be injected in the Spring context.

```java
@EnableMongock
@SpringBootApplicationpublic 
class App {  

	public static void main(String[] args) {    
		new SpringApplicationBuilder()
			.sources(App.class).run(args);  
	}
}
```


----
#### [[Создание скрипта миграции - Mongock - Flashcards|Link to flashcards]]



---
### References:

