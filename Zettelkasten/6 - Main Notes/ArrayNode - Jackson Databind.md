
02-05-2026 14:47

Status:

Tags: [[Jackson]] [[Java+]]

---
# ArrayNode - Jackson Databind

расширяет `JsonNode` 


---
### Получение ArrayNode

Создайте через `ObjectMapper`:
- `ArrayNode array = mapper.createArrayNode()` — пустой массив.

Парсинг JSON: 
- `JsonNode node = mapper.readTree("[1,2,3]"); ArrayNode array = (ArrayNode) node` (проверка `node.isArray()`).sky+1
 


----
## Возможности по сравнению с JsonNode

| ArrayNode                                                                    | JsonNode                                 |
| ---------------------------------------------------------------------------- | ---------------------------------------- |
| `add(примитив)`, `add(JsonNode)`, `addAll(ArrayNode)` — добавление элементов | Только чтение: `get(index)`, `isArray()` |
| `insert(index, значение)` — вставка по позиции                               | Нет мутации массива                      |
| `remove(index/range)` — удаление                                             | Итерация: `elements()`, `iterator()`     |
| `set(index, значение)` — замена                                              | Универсальный для всех типов JSON        |

**Преимущества ArrayNode**: мутируемый API для динамического построения массивов (fluent-style: `array.add("a").add(1)`). 

~={red}`JsonNode` — иммутабельный для чтения/валидации.=~


Пример:

```java
ArrayNode arr = mapper.createArrayNode();
arr.add("item1").add(42).add(mapper.createObjectNode().put("key", "value"));
```

----
#### [[ArrayNode - Jackson Databind - Flashcards|Link to flashcards]]



---
### References:

