
12-09-2026 16:26

Status:

Tags: [[LangChain - Python]] [[Python]]

---
# Parallel Chaining Example - LangChain


Задача: проанализировать фильм и написать пост из двух абзацев: в первом - анализ сюжета, во втором - анализ персонажей.

![[Pasted image 20260912162729.png|444]]


```python
from langchain_openai import ChatOpenAi
from dotenv import load_dotenv
from langchain_core.prompts import ChatPromptTemplate
# Note: 
from langchain.schema.runnable import RunnableLambda, RunnableParallel


load_dotenv()

model = ChatOpenAI( model="gpt-5-nano")


summary_template = ChatPromptTemplate.from_messages(
	[
		{"system", "You are a movie critic."},
		{"human","Provide a brief summary of the movie {movie_name}."},
	]
)

def analyze_plot(plot):
	plot_template = ChatPromptTemplate.from_messages(
		[
			{"system", "You are a movie critic."},
			{"human", "Analyze the plot: {plot}. What are it's strengths and weaknesses?"}
		]
	)
	return plot_template.format_prompt(plot=plot)	


def analyze_characters(characters):
	character_template = ChatPromptTemplate.from_messages(
		[
			("system", "You are a movie critic."),
			("human", "Analyze the characters: {characters}. What are their strengths and weaknesses?")
		]
	)
	return character_template.format_prompt(characters=characters)	

def combine_verdicts(plot_analyzis, character_analyzis):
	return f"Plot analyzis:\n{plot_analyzis}\n\nCharacter Analyzis:\n{character analyzis}"	


plot_branch_chain = {
	RunnableLambda(lambda x: analyze_plot(x)) | model | StrOutputParser()
}

character_branch_chain = {
	RunnableLambda(lambda x: analyze_characters(x)) | model | StrOutputParser()
}

chain = {
	summary_template
	| model
	| StrOutputParser()
	| RunnableParallel(branches={"plot": plot_branch_chain, "characters": characater_branch_chain})
	| RunnableLambda(lambda x: combine_verdict(x["branches"]{"plot"}, x["branches"]["characters"])) # В скобках - пометки, куда какой результат передаём
} 


# Run the chain. Returns already the content
result = chain.invoke({"movie_name": "Inception"})

print(result)
```


----
#### [[Parallel Chaining Example - LangChain - Flashcards|Link to flashcards]]



---
### References:

- [[Sequential Chaining Example - LangChain]]
