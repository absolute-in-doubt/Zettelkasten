
12-09-2026 14:19

Status:

Tags: [[LangChain - Python]] [[Python]]

---
# Types of Messages - LangChain


#### 1. SystemMessage

Defines the AI's role and sets the context for the conversation

_Example: "You are a marketing expert."_


#### 2. HumanMessage

Represents user input or questions directed to the AI.

_Example: "What's a good marketing strategy?"_



#### 3. AIMessage

Contains the AI's responses based on previous messages.

_Example: "Focus on social media engagement"_



----
### Usage


```python
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
```


---
### Prompt Templates


```python
from langchain_openai import ChatOpenAi
from dotenv import load_dotenv
from langchain_core.prompts import ChatPromptTemplate

load_dotenv()

llm = ChatOpenAI( model="gpt-5-nano")

template = "Write a {tone} email to {company} expressing interest in the {position}, mentioning {skill} as a key strength, Keep it to 4 lines max"

promptTemplate = ChatPromptTemplate.from_template(template)

# Заполняет placeholders и формирует готовый промпт
prompt = prompt_template.invoke({
	"tone": "energetic",
	"company": "samsung",
	"position": "AI Engineer",
	"skill": "AI"
})

result = llm.invoke(prompt)

print(result.content)



# IF WE WANT TO MODIFY MULTIPLE MESSAGES (including SystemMessage)

messages = [
	{"system", "You are a comedian who tells jokes about {topic}."},
	{"human", "Tell me {jokes_count} jokes."}
]

prompt_template = ChatPromptTemplate.from_messages(messages)
prompt = prompt_template.invoke(
	{"topic": "lawers",
	"jokes_count": 3}
)

result = llm.invoke(prompt)

print(result.content)
```




----
#### [[Types of Messages - LangChain - Flashcards|Link to flashcards]]



---
### References:

