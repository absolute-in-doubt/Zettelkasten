
04-01-2026 11:41

Status:

Tags: [[Java+]]

---
# Gson

# Gson (Java) — краткое summary


 Gson — это Java-библиотека от Google для преобразования Java-объектов в JSON и обратно (serialization/deserialization).

 Работает с произвольными Java-классами, включая те, к исходникам которых нет доступа.


---
## Ключевые особенности

- Простые методы `toJson()` и `fromJson()` в классе **Gson** для конвертации объектов и строк JSON.
- Поддержка коллекций, дженериков, вложенных и внутренних классов, а также глубоких иерархий наследования.
- Игнорирование лишних полей при десериализации и возможность настраивать формат через пользовательские сериализаторы/десериализаторы и `InstanceCreator`.

## Основные классы

- `Gson` — основной класс: создаётся через `new Gson()` и потокобезопасен для повторного использования.
- `GsonBuilder` — конфигуратор для создания настроенного **Gson** (pretty printing, адаптеры типов, versioning, date‑format, null‑policy и др.).
- `JsonElement`/`JsonObject`/`JsonArray` и др. — дерево JSON для работы на низком уровне без прямого биндинга на Java-классы.

## Типовые сценарии использования

##### Сериализация:
`gson.toJson(obj)` для объектов, коллекций и массивов; результат — JSON-строка или запись в `Writer`.
##### Десериализация:
`gson.fromJson(json, MyClass.class)` 
или с использованием `TypeToken` для дженериков, например списков и карт.[web:4][web:5][web:9]  

##### Низкоуровневый парсинг и изменение JSON

```java
import com.google.gson.*;

public class GsonTreeExample {
    public static void main(String[] args) {
        // Пример входного JSON
        String json = """
            {
              "name": "John",
              "age": 30,
              "phones": ["+1-111-111", "+1-222-222"],
              "address": {
                "city": "New York",
                "zip": "10001"
              }
            }
            """;

        // Парсим строку в дерево JsonElement
        JsonElement rootElement = JsonParser.parseString(json); // корень может быть объектом или массивом
        // Проверяем, что корень — объект и приводим к JsonObject
        if (rootElement.isJsonObject()) { // методы isJsonObject/isJsonArray/isJsonPrimitive/isJsonNull 
            JsonObject rootObj = rootElement.getAsJsonObject();
            // Чтение примитивных полей
            String name = rootObj.get("name").getAsString();
            int age = rootObj.get("age").getAsInt();
            // Чтение вложенного объекта
            JsonObject address = rootObj.getAsJsonObject("address");
            String city = address.get("city").getAsString();
            String zip = address.get("zip").getAsString();
            // Чтение массива
            JsonArray phones = rootObj.getAsJsonArray("phones"); // getAsJsonArray для массива
            for (JsonElement phoneElement : phones) {
                String phone = phoneElement.getAsString();
                System.out.println("Phone: " + phone);
            }
            System.out.println("Name: " + name);
            System.out.println("Age: " + age);
            System.out.println("City: " + city + ", zip: " + zip);
            // Модификация дерева
            // 1) Добавить новое поле
            rootObj.addProperty("active", true); // addProperty добавляет примитив 
            // 2) Изменить существующее поле
            rootObj.addProperty("name", "Johnny");
            // 3) Изменить элемент массива
            phones.set(0, new JsonPrimitive("+1-999-999")); // замена первого элемента 
            // 4) Добавить новый элемент в массив
            phones.add("+1-333-333");
            // 5) Удалить поле из вложенного объекта
            address.remove("zip");
            // Преобразовать обратно в строку JSON
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            String modifiedJson = gson.toJson(rootObj); // сериализация дерева JsonObject 
            System.out.println("Modified JSON:");
            System.out.println(modifiedJson);
        }
    }
}

```


## Настройка и расширение

- Регистрация пользовательских адаптеров типов через `GsonBuilder.registerTypeAdapter(...)` для нестандартных форматов дат, интерфейсов, полиморфных и legacy‑структур.

Опции конфигурации включают:
- pretty printing, 
- стратегии исключения полей, 
	1. Gson не сериализует поля с модификаторами `transient` и `static`
	2. Неизвестные поля в JSON (которых нет в Java-классе) при десериализации просто игнорируются
- политику нулевых значений, 
- контроль версий полей,
- режим «lenient» для более свободного JSON.

## Подключение в проект

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.13.2</version>
</dependency>
```



----
#### [[Gson - Flashcards|Link to flashcards]]



---
### References:

