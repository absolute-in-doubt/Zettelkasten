
15-09-2026 10:12

Status:

Tags: [[LangChain4j]] [[Java+]]

---
# GraphTransformer - LangChain4j


`GraphTransformer` преобразует обычный `Document` в `GraphDocument`, содержащий:

- A set of **nodes** (`GraphNode`)  — сущности/понятия;
- A set of **relationships** (`GraphEdge`) — отношения между ними;
- исходный `Document` как `source`.


The default implementation is `LLMGraphTransformer`, which uses a language model (e.g., OpenAI) to extract graph information from natural language using prompt engineering.

#### Key Benefits

- **Entity and Relationship Extraction**: Identify key concepts and their semantic connections.
- **Graph Representation**: Output is ready for integration into knowledge graphs or graph databases.
- **Model-Powered Parsing**: Uses a large language model to infer structure from unstructured text.

#### Maven Dependency

```
<dependency>  <groupId>dev.langchain4j</groupId>  <artifactId>langchain4j-community-llm-graph-transformer</artifactId>  <version>${latest version here}</version></dependency>
```

#### Example Usage

```
import dev.langchain4j.data.document.Document;import dev.langchain4j.model.openai.OpenAiChatModel;import dev.langchain4j.community.data.document.graph.GraphDocument;import dev.langchain4j.community.data.document.graph.GraphNode;import dev.langchain4j.community.data.document.graph.GraphEdge;import dev.langchain4j.community.data.document.transformer.graph.GraphTransformer;import dev.langchain4j.community.data.document.transformer.graph.llm.LLMGraphTransformer;import java.time.Duration;import java.util.Set;public class GraphTransformerExample {    public static void main(String[] args) {        // Create a GraphTransformer backed by an LLM        GraphTransformer transformer = new LLMGraphTransformer(            OpenAiChatModel.builder()                .apiKey(System.getenv("OPENAI_API_KEY"))                .timeout(Duration.ofSeconds(60))                .build()        );        // Input document        Document document = Document.from("Barack Obama was born in Hawaii and served as the 44th President of the United States.");        // Transform the document        GraphDocument graphDocument = transformer.transform(document);        // Access nodes and relationships        Set<GraphNode> nodes = graphDocument.nodes();        Set<GraphEdge> relationships = graphDocument.relationships();        nodes.forEach(System.out::println);        relationships.forEach(System.out::println);    }}
```

#### Output Example

```
GraphNode(name=Barack Obama, type=Person)GraphNode(name=Hawaii, type=Location)GraphEdge(from=Barack Obama, predicate=was born in, to=Hawaii)GraphEdge(from=Barack Obama, predicate=served as, to=President of the United States)
```

----
#### [[GraphTransformer - LangChain4j - Flashcards|Link to flashcards]]



---
### References:

