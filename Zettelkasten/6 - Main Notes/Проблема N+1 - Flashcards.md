
Theory for the cards: [[Проблема N+1]]

FILE TAGS: java+ java_interview

Q: Будет ли здесь N+1?
```java
@Transactional  
@Retryable(  
        noRetryFor = {DataIntegrityViolationException.class, UserNotFoundException.class},  
        notRecoverable = {DataIntegrityViolationException.class, UserNotFoundException.class},  
        retryFor = {OptimisticLockException.class, TransientDataAccessException.class},  
        maxAttempts = 3,  
        backoff = @Backoff(delay = 100)  
)  
public void createMedia(Media media, Long userId) throws UserNotFoundException{
	User user = userRepository.findById(userId).orElseThrow(() -> new UserNotFoundException("Couldn't find user with id " + userId));
		
	user.getMedia().add(media);  
	media.setUser(user);  
		
	userRepository.save(user);
}
```
A:  Нет, здесь будет просто 2 запроса, т.к. коллекция связанных сущностей Media получается за один запрос. 
	
Например тут было бы N+1:
```java
List<User> users = userRepository.findAll(); // 1 query, returns N users
	
for(User user : users){ //N queries to get media
	user.getMedia(); 
}
```
<!--ID: 1772865200827-->
