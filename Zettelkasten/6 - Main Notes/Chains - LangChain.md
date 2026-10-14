
12-09-2026 15:34

Status:

Tags: [[LangChain - Python]] [[Python]]

---
# Chains - LangChain


### Types of chaining

#### 1. Extended (Sequential) Chaining

Chaining tasks one by one in a straight/sequential line


#### 2. Parallel Chaining 

Lets you run tesks in parallel or simultaniously without being dependent on each other.

![[Pasted image 20260912154257.png]]


#### 3. Conditional Chaining

Lets you run a particular branch based on a condition.

![[Pasted image 20260912154418.png]]



----

### Basic Example

```python
from langchain_openai import ChatOpenAi
from dotenv import load_dotenv
from langchain_core.prompts import ChatPromptTemplate

load_dotenv()

model = ChatOpenAI( model="gpt-5-nano")


messages = [
	("system", "You are a facts expert who knows facts about {animal}."),
	("human", "Tell me {fact_count} facts.")
]

prompt_template = ChatPromptTemplate.from_messages(messages)


# Create the combined chain using LangChain Expression Language (LCEL)
chain = prompt_template | model | StrOutputParser()

# | - это pipe operator. Как только первоы таск (prompt_template) закончится, результат будет передан в следующий -> model -> StrOutputParser()  (instantiates a RunnableSequence)
# по сути на каждом объекте в chain вызывается метод invoke и туда передаются параметры

# StrOutputParser() - функция, которая просто достаёт поле content из result

# Run the chain. Returns already the content
result = chain.invoke({"animal": "cat", "fact_count": 2})
```


---
### How chains work under the hood

`RunnableLambda` - простая обертка, которая позволяет запускать каждый таск как переиспользуемую единицу (single reusable unit).

```python
from langchain_openai import ChatOpenAi
from dotenv import load_dotenv
from langchain_core.prompts import ChatPromptTemplate
# Note: 
from langchain.schema.runnable import RunnableLambda, RunnableSequence


load_dotenv()

model = ChatOpenAI( model="gpt-5-nano")


messages = [
	("system", "You are a facts expert who knows facts about {animal}."),
	("human", "Tell me {fact_count} facts.")
]

prompt_template = ChatPromptTemplate.from_messages(messages)

format_prompt = RunnableLambda(lambda x: prompt_template.format_prompt(**x))
invoke_model = RunnableLambda(lambda x: model.invoke(x.to_messages()))
parse_output = RunnableLambda(lambda x: x.content)


chain = RunnableSequence(first=format_prompt, middle=[invoke_model], last=parse_output)


# Run the chain. Returns already the content
result = chain.invoke({"animal": "cat", "fact_count": 2})
```

----
#### [[Chains - LangChain - Flashcards|Link to flashcards]]



---
### References:

- [[Types of Messages - LangChain#Prompt Templates]]

- [[Sequential Chaining Example - LangChain]]
- [[Parallel Chaining Example - LangChain]]