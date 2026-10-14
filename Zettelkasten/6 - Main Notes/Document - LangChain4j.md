
12-09-2026 19:22

Status:

Tags: [[LangChain4j]] [[Java+]]

---
# Document - LangChain4j

Представляет собой весь документ (Например .md файл или веб страницу).  

На данный момент он поддерживает только текстовую информацию (не поддерживает картинки и таблицы).


### Полезные методы:

- `Document.text()` returns the text of the `Document`
- `Document.metadata()` returns the `Metadata` of the `Document` (see "Metadata" section below)
- `Document.toTextSegment()` converts the `Document` into a `TextSegment` (see "TextSegment" section below)
- `Document.from(String, Metadata)` creates a `Document` from text and `Metadata`
- `Document.from(String)` creates a `Document` from text with empty `Metadata`

----
#### [[Document - LangChain4j - Flashcards|Link to flashcards]]



---
### References:

