
Theory for the cards: [[HttpServlet - jakarta.servlet]]

FILE TAGS: java_interview

Q: Опиши путь запроса в jakarta servlet?
A:   
```
Клиент HTTP-запрос
        ↓
[Connector] (парсит HTTP)
        ↓
[Engine → Host → Context] (матчинг)
        ↓
[Wrapper] (находит сервлет)
        ↓
 ┌─────────────────────┐
 │ FilterChain (вперед)│ ← Filter1.doFilter()
 │ Filter1.doFilter()  │ ← Filter2.doFilter()
 │ Filter2.doFilter()  │ ← FilterN.doFilter()
 │ ...                 │
 │ FilterN.doFilter()  │
 └─────────────────────┘
        ↓ chain.doFilter(req, resp)
 ┌─────────────────────┐
 │   Target Servlet    │ ← service() → doGet()/doPost()
 │ service()           │
 └─────────────────────┘
        ↓
 ┌─────────────────────┐
 │ FilterChain (назад) │ ← FilterN обработка после
 │ FilterN (post)      │ ← Filter2 обработка после  
 │ Filter2 (post)      │ ← Filter1 обработка после
 │ Filter1 (post)      │
 └─────────────────────┘
        ↓
[Connector] → HTTP-ответ → Клиент
```
<!--ID: 1769784246303-->

