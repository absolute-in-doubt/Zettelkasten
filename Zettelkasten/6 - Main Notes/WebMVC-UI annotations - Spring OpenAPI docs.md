
19-02-2026 12:19

Status:

Tags: [[Spring Docs (OpenAPI & Swagger)]]

---
# WebMVC-UI annotations - Spring OpenAPI docs

~={red}Все аннотации опциональны — springdoc сканирует @RequestMapping для базовой spec, но они улучшают читаемость.=~


## Аннотации на уровне класса

- **@Tag**: Группирует операции по тегам. Параметры: name (обязательно), description.  
    Пример: `@Tag(name = "Users", description = "Управление пользователями")`
    
- **@Tag на пакете**: `@Tag(name = "Core", description = "Core APIs")` в package-info.java для глобальной группировки.​
    

## Аннотации на уровне метода

- **@Operation**: Описывает эндпоинт. Ключевые: summary, description, tags, operationId, requestBody.  
    Пример: `@Operation(summary = "Получить пользователя", description = "По ID")`
    
- **@ApiResponses**: Возможные ответы. Вложенные @ApiResponse для кодов (200, 404).  
    Пример: `@ApiResponses({@ApiResponse(responseCode = "200", description = "OK"), @ApiResponse(responseCode = "404", description = "Not found")})`
    
- **@Parameter**: Параметры пути/запроса/заголовка. in = "path/query/header", name, description, required.  
    Пример: `@Parameter(name = "id", description = "User ID")`​
    
- **@RequestBody**: Тело запроса с description, required, content=@Content(schema=@Schema).​
    

## Дополнительные аннотации

| Аннотация                                         | Описание                                     | Применение                                             |
| ------------------------------------------------- | -------------------------------------------- | ------------------------------------------------------ |
| [[@Schema - Spring OpenAPI WebMVC docs\|@Schema]] | Описывает модель (тип, description, example) | На DTO полях или @Schema(implementation=UserDto.class) |
| @SecurityRequirement                              | Добавляет авторизацию (JWT и т.д.)           | На классе/методе                                       |
| @Hidden                                           | Скрывает из docs                             | На методе                                              |




----
#### [[WebMVC-UI annotations - Spring OpenAPI docs - Flashcards|Link to flashcards]]



---
### References:

