
28-04-2026 08:43

Status:

Tags: [[Spring Batch]] [[Spring]]

---
# Reader - Spring Batch


![[Pasted image 20260428084431.png]]

> [!tip] Note
> Работать с брокерами, используя Spring Batch, не рекомендуется. Поскольку Spring Batch предназначен для обработки конечного количества данных.

- `MultiFileReader` - Reader, который может читать набор файлов. Определяет, какие файлы читать, по определенному правилу, например: "filename starts wits 's'". И все файлы, например 100 файлов, который соответствуют этому правилу, **будут выполнены в одном Step.**


---
### Custom `ItemReader`

Настройка готовой реализации _FlatFileItemReader_:

```java
@Bean
@StepScope
public FlatFileItemReader<Animal> csvItemReader(@Value("#{jobParameters['file.input']}") String input) {
    FlatFileItemReaderBuilder<Animal> builder = new FlatFileItemReaderBuilder<>();
    FieldSetMapper<Animal> animalFieldSetMapper = new AnimalFieldSetMapper();

    // @formatter:off
    return builder
            .name("animalReader")
            .resource(new FileSystemResource(input))
            .delimited()
            .names(TOKENS)
            .fieldSetMapper(animalFieldSetMapper)
            .build();
    // @formatter:on
}
```

AnimalFieldSetMapper:

```java
public class AnimalFieldSetMapper  implements FieldSetMapper<Animal> {

    @Override
    public Animal mapFieldSet(FieldSet fieldSet) throws BindException {
        Animal animal = new Animal();
        animal.setId(fieldSet.readString("id"));
        animal.setName(fieldSet.readString("name"));
        return animal;
    }
}
```



----
#### [[Reader - Spring Batch - Flashcards|Link to flashcards]]



---
### References:

