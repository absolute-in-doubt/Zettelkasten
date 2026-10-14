
30-09-2026 13:01

Status:

Tags: [[Prompt engineering]] [[LangChain4j]] [[LangChain - Python]] [[Java+]]

---
# Advanced Prompt Patterns - Prompt Engineering


> [!note]
> В примерах промптов используются xml теги, поскольку ChatGPT уммет их интерпретировать.
> ННасколько это эффективно для других моделей - надо проверять.


### Low & High Eagerness



![[Pasted image 20260930130259.png]]

- **Low Eagerness** - be lazy & just give me the answer real quick

Пример Low Eagerness prompt template
```
<context_gathering>
    - Search depth: very low    
    - Bias strongly towards providing a correct answer as quickly as possible   
    - Usually, this means an absolute maximum of 2 reasoning steps    
    - If you think you need more time, state what you know and what's uncertain    
</context_gathering>        

Problem: %s  
Provide your answer:  
```


**High Eagerness** - prolonged reasoning

Пример High Eagerness prompt template:
```
Analyze this problem thoroughly and provide a comprehensive solution.  
Consider multiple approaches, trade-offs, and important details.  
Show your analysis and reasoning in your response.  
  
Problem: %s
```


---
### Task Execution Pattern

Plan is usually a prelimiary step performed by AI itself.

1. Plan
2. Execute
3. Summarize

Provides clear preambles and progress updates for better UX. (Логичнее использовать со стриминговой моделью типа `OpenAiOfficialStreamingChatModel`, которая возвращает не весь ответ в конце а по ходу того, как модель доставляет данные)

```
<task_execution>  
1. First, briefly restate the user's goal in a friendly way  
  
2. Create a step-by-step plan:  
	- List all steps needed   
	- Identify potential challenges   
	- Outline success criteria  
3. Execute each step:  
	- Narrate what you're doing   
	- Show progress clearly
	- Handle any issues that arise  
4. Summarize:  
	- What was completed   
	- Any important notes   
	- Next steps if applicable
</task_execution>  
  
<tool_preambles>  
- Always begin by rephrasing the user's goal clearly  
- Outline your plan before executing  
- Narrate each step as you go  
- Finish with a distinct summary  
</tool_preambles>  
  
Task: %s  
  
Begin execution:
```

---
#### Self-Reflecting


![[Pasted image 20260930130733.png]]


Пример простого Self-Reflecting Prompt с internal evaluation (нет внешних бенчмарков):
```
Generate Java code with production-quality standards: %s  
Keep it simple and include basic error handling.
```

**Оценка может быть не единствнной, а составной: Structured Analysis:**

Например:
1. Correctness (Logic and functionality) - ~={green}Pass=~
2. Best Practices (Code quality) - ~={yellow}Minor=~
3. Performance (Efficiency concernes) - ~={orange}Moderate=~
4. Security (Vulnerabilities) - ~={red}Critical=~

```
<analysis_framework>  
You are an expert code reviewer. Analyze the code for:  
  
1. Correctness  
	- Does it work as intended?   
	- Are there logical errors?  
2. Best Practices  
	- Follows language conventions?   
	- Appropriate design patterns?  
3. Performance  
	- Any inefficiencies?   
	- Scalability concerns?  
4. Security  
	- Potential vulnerabilities?   
	- Input validation?  
5. Maintainability  
	- Code clarity?   
	- Documentation?  
<output_format>  
Provide your analysis in this structure:  
- Summary: One-sentence overall assessment  
- Strengths: 2-3 positive points  
- Issues: List any problems found with severity (High/Medium/Low)  
- Recommendations: Specific improvements  
</output_format>  
</analysis_framework>  
  
Code to analyze:  
```  
%s  
```  
  
Provide your structured analysis:
```


---
### Constrained Output

Например:

- Exactly 100 words
- Bulletpoints
- 3 sections
- No jargon

```
<strict_constraints>  
You MUST adhere to these constraints:  
- Topic: %s  
- Format: %s  
- Maximum words: %d  
- Do NOT exceed the word limit  
- Do NOT deviate from the specified format  
</strict_constraints>  
  
<quality_requirements>  
Within the constraints:  
- Be informative and accurate  
- Use clear, professional language  
- Organize content logically  
- Include relevant details  
</quality_requirements>  
  
Generate the content:
```

---
#### Multi-turn chat

По сути промпт для обычного чата с сохранением памяти. 

```
You are a helpful assistant in an ongoing conversation.  
  
<conversation_guidelines>  
- Remember previous context from this session  
- Build on earlier discussion points naturally  
- Ask clarifying questions when truly needed  
- Maintain consistent personality and tone throughout  
- Reference prior exchanges when relevant  
</conversation_guidelines>  
  
<response_style>  
- Be concise but complete  
- Use examples when they help understanding  
- Format responses clearly with proper structure  
- Acknowledge the user's previous messages when relevant  
- Show that you understand the conversation flow  
</response_style>  
  
<tool_preambles>  
- If you need to perform multiple steps, outline your plan first  
- Provide updates as you work through complex requests  
- Summarize what you've done at the end  
</tool_preambles>
```


----
#### [[Advanced Prompt Patterns - Prompt Engineering - Flashcards|Link to flashcards]]



---
### References:

