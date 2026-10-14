
Index: [[LangChain4j]] [[RAG - LangChain]] [[RAG]] [[Java+]]

```xml
<dependency>
    <groupId>dev.langchain4j</groupId>
    <artifactId>langchain4j-easy-rag</artifactId>
    <version>1.20.0-beta30</version>
</dependency>
```

- [[Easy RAG - LangChain4j]]



#### Classes

```
                         RAG
                          │
          ┌───────────────┴───────────────┐
          │                               │
       Indexing                         Retrieval
          │                               │
          ▼                               ▼
      Document                         Query
          │                               │
          ▼                               ▼
     TextSegment                  EmbeddingModel
          │                               │
          ▼                               ▼
    EmbeddingModel                  Vector Search
          │                               │
          ▼                               ▼
      Embedding                  ContentRetriever
          │                               │
          └───────────┬───────────────────┘
                      ▼
                  Context
                      │
                      ▼
                  ChatModel
                      │
                      ▼
                    LLM
```

###### Indexing (Calculating embeddings)

![[Pasted image 20260915100247.png|410]]

- [[Document - LangChain4j]]
- [[Metadata - LangChain4j]]
- [[Загрузка документов (DocumentLoader, DocumentParser) - LangChain4j]]
- [[DocumentTransformer - LangChain4j]]
- [[GraphTransformer - LangChain4j]]




##### Retrieval

![[Pasted image 20260915100308.png|547]]





---
https://docs.langchain4j.dev/tutorials/rag/