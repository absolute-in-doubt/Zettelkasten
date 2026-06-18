
18-04-2026 11:51

Status:

Tags: [[Spring Batch]] [[Spring]]

---
# Job - Spring Batch

> [!warning]
> По умолчанию сконфигурированное задание _Job_ запускается на старте приложения, но часто это неудобно.  Чтобы отключить автозапуск, в настройках зададим:
> ```properties
> spring.batch.job.enabled=false
> ```


Job считывает и обрабатывает по одному и записывает чанком. Обработка чанка происходит в транзакции, если транзакция фейлится - данные не теряются.

---
#### Создание Job

```java
@Bean
public Job job(Step step) {
    return jobs.get("job")
            .start(step)
            .build();
}
```

В Job указываем:
- Steps


---
#### Создание Step

```java
@Bean
public Step step(ItemReader<Animal> csvItemReader, ItemWriter<Animal> jsonItemWriter) throws IOException {
    // @formatter:off
    return steps
            .get("step1")
            .<Animal, Animal>chunk(3) // размер батча, который считываем
            .reader(csvItemReader)
            .processor(animalItemProcessor())
            .writer(jsonItemWriter)
            .build();
    // @formatter:on
}
```



----
### `ItemReader`

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
### `ItemProcessor`

Отвечает именно за обработку данных.

_ItemProcessor_ у нас просто при водит имя к верхнему регистру:

```java
@Bean
@StepScope
ItemProcessor<Animal, Animal> animalItemProcessor() {
    return animal -> {
        LOGGER.info("Processing " + animal.getName());
        animal.setName(animal.getName().toUpperCase());
        return animal;
    };
}
```

> [!warning] Note
> **`ItemProcessor` НЕ обязателен** для простого переноса данных из одного источника в другой. Spring Batch позволяет пропустить processor, если преобразования не требуются.

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
#### [[Job - Spring Batch - Flashcards|Link to flashcards]]



---
### References:

