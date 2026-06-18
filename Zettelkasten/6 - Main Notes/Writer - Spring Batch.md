
28-04-2026 08:52

Status:

Tags: [[Spring Batch]] [[Spring]]

---
# Writer - Spring Batch

![[Pasted image 20260428085323.png]]

> [!tip] Note
> В отличие от чтения, записывать в брокеры - вполне канонично и безопасно.


---
### `ItemWriter`

Настройка готовой реализации _JsonFileItemWriter_:

```java
@Bean
@StepScope
public JsonFileItemWri"#{jobParameters['file.output']}") String output) throws IOException {
    JsonFileItemWriterBuilder<Animal> builder = new JsonFileItemWriterBuilder<>();
    JacksonJsonObjectMarshaller<Animal> marshaller = new JacksonJsonObjectMarshaller<>();

    // @formatter:off
    return builder
            .name("animalWriter")
            .jsonObjectMarshaller(marshaller)
            .resource(new FileSystemResource(output))
            .build();
    // @formatter:on
}
```



----
#### [[Writer - Spring Batch - Flashcards|Link to flashcards]]



---
### References:

