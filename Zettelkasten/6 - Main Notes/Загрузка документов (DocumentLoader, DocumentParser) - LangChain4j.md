
15-09-2026 10:03

Status:

Tags: [[LangChain4j]] [[Java+]] [[LangChain - Python]]

---
# DocumentLoader - LangChain4j



You can create a `Document` from a `String`, but a simpler method is to use one of our document loaders included in the library:

- `FileSystemDocumentLoader` from the `langchain4j` module
- `ClassPathDocumentLoader` from the `langchain4j` module
- `UrlDocumentLoader` from the `langchain4j` module
- `AmazonS3DocumentLoader` from the `langchain4j-document-loader-amazon-s3` module
- `AzureBlobStorageDocumentLoader` from the `langchain4j-document-loader-azure-storage-blob` module
- `GitHubDocumentLoader` from the `langchain4j-document-loader-github` module
- `GoogleCloudStorageDocumentLoader` from the `langchain4j-document-loader-google-cloud-storage` module
- `SeleniumDocumentLoader` from the `langchain4j-document-loader-selenium` module
- `PlaywrightDocumentLoader` from the `langchain4j-document-loader-playwright` module
- `TencentCosDocumentLoader` from the `langchain4j-document-loader-tencent-cos` module


---
### Document Parser

`Document`s can represent files in various formats, such as PDF, DOC, TXT, etc. To parse each of these formats, there's a `DocumentParser` interface with several implementations included in the library:

- `TextDocumentParser` from the `langchain4j` module, which can parse files in plain text format (e.g. TXT, HTML, MD, etc.)
- `ApachePdfBoxDocumentParser` from the `langchain4j-document-parser-apache-pdfbox` module, which can parse PDF files
- `ApachePoiDocumentParser` from the `langchain4j-document-parser-apache-poi` module, which can parse MS Office file formats (e.g. DOC, DOCX, PPT, PPTX, XLS, XLSX, etc.)
- `ApacheTikaDocumentParser` from the `langchain4j-document-parser-apache-tika` module, which can automatically detect and parse almost all existing file formats
- `DoclingDocumentParser` from the `langchain4j-document-parser-docling` module, which uses [Docling Java](https://docling-project.github.io/docling-java/current/) and [Docling](https://docling.ai/) to process documents.
- `MarkdownDocumentParser` from the `langchain4j-document-parser-markdown` module, which can parse files in markdown format
- `YamlDocumentParser` from the `langchain4j-document-parser-yaml` module, which can parse files in yaml format


----
### Example

```java
// Load a single document
Document document = FileSystemDocumentLoader.loadDocument("/home/langchain4j/file.txt", new TextDocumentParser());

// Load all documents from a directory
List<Document> documents = FileSystemDocumentLoader.loadDocuments("/home/langchain4j", new TextDocumentParser());

// Load all *.txt documents from a directory
PathMatcher pathMatcher = FileSystems.getDefault().getPathMatcher("glob:*.txt");
List<Document> documents = FileSystemDocumentLoader.loadDocuments("/home/langchain4j", pathMatcher, new TextDocumentParser());

// Load all documents from a directory and its subdirectories
List<Document> documents = FileSystemDocumentLoader.loadDocumentsRecursively("/home/langchain4j", new TextDocumentParser());
```


You can also load documents without explicitly specifying a `DocumentParser`. In this case, a default `DocumentParser` will be used. The default one is loaded through SPI (e.g. from `langchain4j-document-parser-apache-tika` or `langchain4j-easy-rag`, if one of them is imported). If no `DocumentParser`s are found through SPI, a `TextDocumentParser` is used as a fallback.


----
#### [[DocumentLoader - LangChain4j - Flashcards|Link to flashcards]]



---
### References:

