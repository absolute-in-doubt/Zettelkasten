
12-09-2026 16:14

Status:

Tags: [[LangChain - Python]] [[Python]]

---
# Sequential Chaining Example - LangChain


```python
from langchain_openai import ChatOpenAi
from dotenv import load_dotenv
from langchain_core.prompts import ChatPromptTemplate
# Note: 
from langchain.schema.runnable import RunnableLambda, RunnableSequence


load_dotenv()

model = ChatOpenAI( model="gpt-5-nano")


animal_facts_template = ChatPromptTemplate.from_messages([
	("system", "You are a facts expert who knows facts about {animal}."),
	("human", "Tell me {fact_count} facts.")
])

translation_template = ChatPromptTemplate.from_messages(
[
	{"system", "You are a translater and convert the provided text into {language}."},
	{"human", "Translate the following text to {language}: {text}"}
])

count_words = RunnableLambda(lambda x: f"Word count: {len(x.split())}\n{x}")
prepare_for_translateion = Runnable(lambda output: {"text": output, "language": "french"})

# LCEL
chain = animal_facts_template | model | StrOutputParser() | prepare_for_translateion | translation_template | model | StrOutputParser()

# Run the chain. Returns already the content
result = chain.invoke({"animal": "cat", "fact_count": 2})

print(result)
```


----
#### [[Sequential Chaining Example - LangChain - Flashcards|Link to flashcards]]



---
### References:

- [[Parallel Chaining Example - LangChain]]