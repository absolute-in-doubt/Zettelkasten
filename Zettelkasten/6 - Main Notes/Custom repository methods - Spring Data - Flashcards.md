
Theory for the cards: [[Custom repository methods - Spring Data]]

FILE TAGS: java_interview spring

Q: Что делает аннотация @Modifying в Spring?
A: Аннотация `@Modifying` в Spring Data JPA указывает, что метод репозитория выполняет модифицирующие операции (UPDATE, DELETE, INSERT или DDL), а не только чтение данных.
	
**Основная роль**
	
По умолчанию методы Spring Data JPA считаются read-only. `@Modifying` вместе с `@Query` позволяет выполнять изменения в БД, активируя flush контекста персистентности и обеспечивая транзакционную целостность.
	
**Ключевые параметры**
	
- `flushAutomatically = true` — принудительно сбрасывает изменения в БД перед выполнением запроса.
    
- `clearAutomatically = true` — очищает persistence context после выполнения (для bulk-операций).​
    
**Пример использования**
	
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    
    @Modifying
    @Query("UPDATE User u SET u.active = false WHERE u.id = :id")
    void deactivateUser(@Param("id") Long id);
}
```
	
Без `@Modifying` такой метод не сработает — Spring выдаст ошибку.
<!--ID: 1770904340940-->
