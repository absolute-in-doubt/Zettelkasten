
30-09-2026 13:00

Status:

Tags: [[Prompt engineering]]  [[LangChain4j]] [[LangChain - Python]] [[Java+]]

---
# Simple Prompt Patterns - Prompt Engineering




![[Pasted image 20260930122933.png]]

#### Zero-shot

Простой вопрос - ответ, не требующий предобучения:

**Input:** "Classify sentiment: I love it!"
**Output:** "Positive"

---
#### Few-shot

Learn patterns from the provided examples.
Вопрос, требующий небольшого предобучения - контекста с примерами:

**Input:**  "Sentiment classification examples: Example1: Good product - Positive. Example2: Nothing special - Neutral. Terrible - Negative" после этого - запрос: "Classify sentiment: The service was slow."
**Output:** "Negative"

---
#### Chain of thoughts

![[Pasted image 20260930123501.png]]


---
#### Role-Based

Задаем роль и модель pretends to be that perrrson. Но у этого есть ограничения, ведь экспертности это не добавляет, а скорее ограничивает модель. Этот подход можно использовать, когда нужен определенный тон ответа, например, чтобы модель отвечала как ученый для написания научной статьи.

----
#### [[Simple Prompt Patterns - Prompt Engineering - Flashcards|Link to flashcards]]



---
### References:

