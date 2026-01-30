
16-01-2026 12:21

Status: #adult 

Tags: [[Java Core]]

---
# Порядок вызова конструкторов и initializer blocks - Java Core

1. Parent static block(s) 
2. Child static block(s) 

> [!tip] Note
> `static initializer blocks` выполняются в [[Detailed Initialization Procedure - Java|процессе инициализации класса]]. В том порядке в котором они написаны в коде.

3. Parent non-static block(s) 
4. Parent constructor
5. Child non-static block(s) 
6. Child constructor 

> [!note] Note
> Обычные `initializer blocks` тоже выполняются в том порядке, в котором они написаны в классе

### Выбрасывание ошибок в intializer blocks

##### `static intializer block`

Для статического блока выбрасывание исключения в явном виде, приводит к ошибке компиляции.

##### `initializer block`

Для нестатических блоков инициализации, если выбрасывание исключения прописано явным образом требуется, чтобы объявления этих исключений были перечислены в `throws` всех конструкторов класса. Иначе будет ошибка компиляции. 


----
#### [[Порядок вызова конструкторов и initializer blocks - Java Core - Flashcards|Link to flashcards]]



---
### References:

