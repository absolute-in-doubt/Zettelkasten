
12-09-2026 14:51

Status:

Tags: [[LangChain - Python]] [[Python]]

---
# Simple terminal chat example - LangChain


```python
from langchain_google_genai import ChatGoogleGenerativeAi
from langchain_anthropic import ChatAnthropic
from langchain_openai import ChatOpenAi
from dotenv import load_dotenv
from langchain_core.messages import SystemMessage, HumanMessage, AIMessage

load_dotenv()

model = ChatAnthropic( model="claude-haiku-4-5-20251001")

chat_history = [] # Use a list to store messages

# Setting an initial system message (optional)
system_message = SystemMessage("You are a helpfull AI asistant")

# Chat loop
while True:
	query = input("You: ")
	if query.lower() == "exit":
		break
	chat_history.append(HumanMessage(content=query)) # Add user message
	
	# Get AI response using history
	result = model.invoke(chat_history)
	response = result.content
	chat_history.append(AIMessage(content=response)) # Add AI message
	print(f"AI response: {response.content}")


print("-----Message history------")
print(chat_history)
```



----
#### [[Simple terminal chat example - LangChain - Flashcards|Link to flashcards]]



---
### References:

