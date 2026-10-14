
12-09-2026 18:36

Status:

Tags: [[LangChain4j]] [[Java+]] 

---
# Easy RAG - LangChain4j

LangChain4j has an "Easy RAG" feature that makes it as easy as possible to get started with RAG. You don't have to learn about embeddings, choose a vector store, find the right embedding model, figure out how to parse and split documents, etc. Just point to your document(s), and LangChain4j will do its magic.


### 1. Load the documents

```java
List<Document> documents = FileSystemDocumentLoader.loadDocuments("/home/langchain4j/documentation");
```

> The Apache Tika library, which supports a wide variety of document types, is used to detect document types and parse them. Since we did not explicitly specify which `DocumentParser` to use, the `FileSystemDocumentLoader` will load an `ApacheTikaDocumentParser`, provided by `langchain4j-easy-rag` dependency through SPI.


##### What is happening under the hood?

If you want to load documents from all subdirectories, you can use the `loadDocumentsRecursively` method:

```java
List<Document> documents = FileSystemDocumentLoader.loadDocumentsRecursively("/home/langchain4j/documentation");
```

Additionally, you can filter documents by using a glob or regex:

```java
PathMatcher pathMatcher = FileSystems.getDefault().getPathMatcher("glob:*.pdf");List<Document> documents = FileSystemDocumentLoader.loadDocuments("/home/langchain4j/documentation", pathMatcher);
```


> [!note]
> When using `loadDocumentsRecursively` method, you might want to use a double asterisk (instead of a single one) in glob: `glob:**.pdf`.

---




### 2. Создание Embedding Store, Расчет embedding-ов, помещаем их в store

We can use any of our 30+ [supported embedding stores](https://docs.langchain4j.dev/integrations/embedding-stores), but for simplicity, we will use an in-memory one:

```java
InMemoryEmbeddingStore<TextSegment> embeddingStore = new InMemoryEmbeddingStore<>();
EmbeddingStoreIngestor.ingest(documents, embeddingStore);
```

##### What is happening under the hood?

1. The `EmbeddingStoreIngestor` loads a `DocumentSplitter` from the `langchain4j-easy-rag` dependency through SPI. Each `Document` is split into smaller pieces (`TextSegment`s) each consisting of no more than 300 tokens and with a 30-token overlap.
    
2. The `EmbeddingStoreIngestor` loads an `EmbeddingModel` from the `langchain4j-easy-rag` dependency through SPI. Each `TextSegment` is converted into an `Embedding` using the `EmbeddingModel`.
    

> [!note]
>
 Default embedding model -  [bge-small-en-v1.5](https://huggingface.co/BAAI/bge-small-en-v1.5). It has achieved an impressive score on the [MTEB leaderboard](https://huggingface.co/spaces/mteb/leaderboard), and its quantized version occupies only 24 megabytes of space. Therefore, we can easily load it into memory and run it in the same process using [ONNX Runtime](https://onnxruntime.ai/).
>
>**~={orange}Yes, that's right, you can convert text into embeddings entirely offline, without any external services, in the same JVM process.=~** LangChain4j offers some popular embedding models [out-of-the-box](https://docs.langchain4j.dev/integrations/embedding-models/in-process).

3. All `TextSegment`-`Embedding` pairs are stored in the `EmbeddingStore`.


---
### 4. Создание AI Service

[AI Service](https://docs.langchain4j.dev/tutorials/ai-services) - абстракция для взаимодействия с LLM по API.

```java
interface Assistant {

    String chat(String userMessage);
}

ChatModel chatModel = OpenAiChatModel.builder()
    .apiKey(System.getenv("OPENAI_API_KEY"))
    .modelName(GPT_4_O_MINI)
    .build();

Assistant assistant = AiServices.builder(Assistant.class)
    .chatModel(chatModel)
    .chatMemory(MessageWindowChatMemory.withMaxMessages(10)) 
    .contentRetriever(EmbeddingStoreContentRetriever.from(embeddingStore))
    .build();
```


`MessageWindowChatMemory.withMaxMessages(10)` - sliding window of 10 last messages


---
### 5. Готово:

```java
String answer = assistant.chat("How to do Easy RAG with LangChain4j?");
```


----
#### [[Easy RAG - LangChain4j - Flashcards|Link to flashcards]]



---
### References:

