
12-09-2026 17:19

Status:

Tags: [[LangChain - Python]] [[Python]] [[AI Engineering]]

---
# RAG - LangChain


### Part 1: chunking, calculating embeddings and putting them into the Vector DB


Используем Chroma в качестве Vector DB.

В [[Venv - Python|venv]]:

```shell
pip install chromadb

# Wrapper that connects our langchain application with the vector store
pip install langchain-chroma
```


```python

import getpass
import os
from langchain.text_splitter import CharacterTextSplitter
from langchain_community.document_loaders import TextLoader
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings

# Difine the directory containing the text file and the persistent directory
current_dir = os.path.dirname(os.path.abspath(__file__))
file_path = os.path.join(current_dir, "documents", "lord_of_the_rings.txt")
persistent_directory = os.path.join(current_dir, "db", "chroma_db")

# Check if the Chroma vector store already exists
if not os.path.exists(persistent_directory):
	print("Persistent directory doesn't exist. Initializing vector store...")
	
	# Ensure the text file exists
	if not os.path.exists(file_path):
		raise FileNOtFoundError(
			f"The file {file_path} doesn't exist. Please check the path"
		)
		
	# Read the text content from the file
	loader = TextLoader(file_path)
	documents = loader.load()
	
	# Split the document into chunks
	# chunk_size = 1000 tokens
	text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=50)
	docs = text_splitter.split_documents(documents)
	
	# Display information about the split documents
	print("\n------ Document Chunks Information ----")
	print(f"Number of document chunks: {len(docs)}")
	print(f"Sample chunk:\n{docs[0].page_content}\n")	
	
	
	# Create embeddings
	print("\n---- Creating embeddingd -----")
	embeddings = OpenAIEmbeddingd(
		model="text-embeddding-3-small"
	) # Update to a valid embedding model if needed
	print("\n--- Finished creating embeddings ----")
	
	print("\n---- Creating vector store -----")
	db = Chroms.from_documents(
		docs, embeddings, persist_directory=persistent_directory
	)
	print("\n---- Finished creating vector store ----")
	
else:
	print("Vector store already exists. No need to initialize.")

```


- **Chunk Overlap** - это количество символов ()или токенов, в зависимости от splitter-а), которое включается и в конец предыдущего чанка и в начало следующего. ~={orange}**Это помогает сохранить контекст между чанками**=~
	_Например, если `chunk_overlap = 100`, то последние 100 символов одного чанка появятся в начале следующего чанка._ 





----
#### [[RAG - LangChain - Flashcards|Link to flashcards]]



---
### References:

- [[RAG]]