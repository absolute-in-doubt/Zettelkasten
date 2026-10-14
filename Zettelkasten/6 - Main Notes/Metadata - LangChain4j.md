
12-09-2026 19:24

Status:

Tags: [[LangChain4j]] [[Java+]]

---
# Metadata - LangChain4j

Каждый [[Document - LangChain4j|Document]] содержит `Metadata`. 

`Metadata` хранит информацию о `Document`, такую как имя, источник, lasst update date, owner и т.д.

`Metadata` представляет собой key-value map, где ключ имеет тип `String`, а значение может быть следующих типов: `String`, `Integer`, `Long`, `Float`, `Double`, `UUID`.


`Metadata` может быть полезна в следующих случаях:

- Когда передаём содержимое документа в LLM, можно включить `Metadata`, чтобы LLM получила дополнительную информацию. Например, предоставление LLM имени файла и его источника может помочь ей лучше понять содержимое.

- По `Metadata` можно фильтровать релевантные файлы, которые хотим добаавить в промпт. Например, можно сузить поиск подходящих `Document` до принадлежащих определенному owner.

- Когда источник документа обновляется (например, определенная страница документации), можно быстро определить что за `Document` это по `Metadata` entry и обновить его в `EmbeddingStore`, для поддержания его в up-to-date state.


---
### Useful methods

- `Metadata.from(Map)` creates `Metadata` from a `Map`
- `Metadata.put(String key, String value)` / `put(String, int)` / etc., adds an entry to the `Metadata`
- `Metadata.putAll(Map)` adds multiple entries to the `Metadata`
- `Metadata.getString(String key)` / `getInteger(String key)` / etc., returns a value of the `Metadata` entry, casting it to the required type
- `Metadata.containsKey(String key)` checks whether `Metadata` contains an entry with the specified key
- `Metadata.remove(String key)` removes an entry from the `Metadata` by key
- `Metadata.copy()` returns a copy of the `Metadata`
- `Metadata.toMap()` converts `Metadata` into a `Map`
- `Metadata.merge(Metadata)` merges the current `Metadata` with another `Metadata`

----
#### [[Metadata - LangChain4j - Flashcards|Link to flashcards]]



---
### References:

