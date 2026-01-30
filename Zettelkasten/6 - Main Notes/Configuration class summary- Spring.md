
25-11-2025 14:52

Status: currently updated

Tags: [[Spring]]

---
# Configuration class summary- Spring

#### Core:






### Data JPA

[[setup - Spring Data|@EnableJpaRepositories]]~={purple}(`path to repositories`)=~

~={purple}@EntityScan(`path to entities`)=~ - нужно только, если главный класс - не в корне проекта




## Notes on configuration with Spring Boot

- В ~={purple}@SpringBootApplication=~ уже _встроена_ ~={purple}@ComponentScan=~, которая по умолчанию сканирует пакет, где лежит главный класс приложения, и все его подпакеты.




----
#### [[Configuration class summary- Spring - Flashcards|Link to flashcards]]



---
### References:

