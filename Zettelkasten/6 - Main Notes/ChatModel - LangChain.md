
12-09-2026 14:26

Status:

Tags: [[LangChain - Python]] [[Python]]

---
# ChatModel - LangChain


В LangChain под каждый тип ИИ своя реализация ClientChat и они отличаются по возможностям, например какие-то не поддерживают json.

Подробнее про возможности здесь:
[Chat model integrations - Docs by LangChain](https://docs.langchain.com/oss/python/integrations/chat)


### Example

Например мы хотим использовать ChatOpenAI, для этого мы импортируем пакет `langchain_openai`:

```shell
pip install -qU langchain-openai
```


> [!note]
> Требует присутствия `OPENAI_API_KEY` в Environment. Импортировать его можно при помощи пакета [[dotenv - Python lib|dotenv]]


```python
from langchain_openai import ChatOpenAi

# параметр model - единственный обязательный

llm = ChatOpenAI( model="gpt-5-nano", 
	# stream_usage=True, 
	# temperature=None, 
	# max_tokens=None, 
	# timeout=None, 
	# reasoning_effort="low", 
	# max_retries=2, 
	# api_key="...", # If you prefer to pass api key in directly 
	# base_url="...", 
	# organization="...", 
	# other params... )
	
	
# Сам запрос:
result = llm.invoke("What is the square root of 49?") 


print(result.content)
```

`result` - содержит множество параметров помимо `content`, как например метаданные, кол-во использованных токенов, id запроса и т.д.

```python
from langchain_google_genai import ChatGoogleGenerativeAi
from langchain_anthropic import ChatAnthropic
from langchain_openai import ChatOpenAi
from dotenv import load_dotenv
from langchain_core.messages import SystemMessage, HumanMessage, AIMessage

load_dotenv()

message = [
	SystemMessage("You are an expert in social media content strategy"),
	HumanMessage("Give a short tip to create engaging posts on Instagram")
]

llm = ChatOpenAI( model="gpt-5-nano")
	
	
# Сам запрос:
result = llm.invoke(message) 


print(result.content)


# ------------- Anthropic -------------------


model = ChatAnthropic( model="claude-haiku-4-5-20251001")

result = model.invoke(message)

print(result.content)
```

----
### Switching Between Models





----
#### [[ChatModel - LangChain - Flashcards|Link to flashcards]]



---
### References:

