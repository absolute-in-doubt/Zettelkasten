
Index: [[Java+]]

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.42</version>
    <scope>provided</scope>
</dependency>
```

##### Enabling annotation processing:

- Откройте **File > Settings** 
    
- Перейдите в раздел **Build, Execution, Deployment > Compiler > Annotation Processors**.
    
- Поставьте галочку напротив **Enable annotation processing**.
    
- Настройте дополнительные опции: **Obtain processors from project classpath** (для процессоров из classpath проекта) или укажите **Processor path** (для внешних JAR-файлов).


**~={purple}@Data=~**:
```
@Getter
@Setter
@ToString
@EqualsAndHashCode
@RequiredArgsConstructor
```

----

[[Аннотации - Lombok]]