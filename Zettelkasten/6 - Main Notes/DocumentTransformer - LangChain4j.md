
15-09-2026 10:07

Status:

Tags: [[LangChain4j]] [[Java+]] 

---
# DocumentTransformer - LangChain4j


`DocumentTransformer` implementations can perform a variety of document transformations such as:

- Cleaning: This involves removing unnecessary noise from the `Document`'s text, which can save tokens and reduce distractions.
- Filtering: to completely exclude particular `Document`s from the search.
- Enriching: Additional information can be added to `Document`s to potentially enhance search results.
- Summarizing: The `Document` can be summarized, and its short summary can be stored in the `Metadata` to be later included in each `TextSegment` (which we will cover below) to potentially improve the search.
- Etc.

`Metadata` entries can also be added, modified, or removed at this stage.

На данный момент есть только реализация `HtmlToTextDocumentTransformer` в  `langchain4j-document-transformer-jsoup` module, который может извлекать текст и метаданные из raw HTML.

**Since there is no one-size-fits-all solution, we recommend implementing your own `DocumentTransformer`, tailored to your unique data.**


---
### Example

Например use case: строим RAG по базе знаний в Obsidian - ссылки между файлами нужно обработать. Это лучше всего делать в custom `DocumentTransformer`:


`DocumentTransformer` как раз предназначен для изменения/обогащения `Document` и его metadata; LangChain4j прямо рекомендует писать собственные transformers под специфическую задачу

```java
public class ObsidianLinkDocumentTransformer
        implements DocumentTransformer {

    @Override
    public Document transform(Document document) {

        Set<String> links = extractWikiLinks(document.text());

        document.metadata()
                .put("obsidian.links", String.join(",", links));

        return document;
    }
}
```


----
#### [[DocumentTransformer - LangChain4j - Flashcards|Link to flashcards]]



---
### References:

