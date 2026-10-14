
26-09-2026 19:16

Status:

Tags: [[GraphQL]] [[Networks]] [[Java+]]

---
# GraphQL vs REST - когда что использовать

GraphQL - transport agnostic.

GraphQL состоит из:

- **Schema** - хема доступных данных, которую можно запрашивать через Queries.
- **Query**
- **Nesting** - например: возвращаем пост с вложенными в него первыми 5 комментариев
- **Mutation*** - POST запрос для изменения данных на сервере
- **Subscription** - клиент может подписаться на изменения, которые будут возвращаться с определенной денормализованной схемой. 
	Например, первый пост пользователя и самые популярные 3 комментария в одном response и сервер их отправляет каждый раз, когда меняются самые популярные комментарии или выходит новый пост пользователя.



Плюсы

- Flexibility
- efficient response : payload back only get what you want of fields since you know the schema
- No round trips- Avoiding multiple round trips (HATEOS REST)
- Uniform single interface API endpoint
- Self documenting

Минусы 

- Complexity
- Typed system - ( use it to know if a type is available or not and fork logic) slows down adoption.. same as soap
- No Caching etag since always POST
- Error management non-standard for HTTP.
- Over engineering can lead to Inefficiency of the joins can lead to performance and DOS




---
### Пример

В facebook есть отображение того, что твои друзья лайкнули какой-то пост.

В обычном REST чтобы это сделать понадобислось бы:

GET /posts/{id}

GET /post/{id}/likes/amount

GET /post/{id}/likes (returns full info about each like - who liked)

GET /post/{id}/comments/amount

Много round trips для отображения одного поста. А также они возвращают не только нужные поля, но и не нужные: id поста, метаданные, и т.д.

Чтобы запрашивать только нужные properties - создали **Query Language**.



----
#### [[GraphQL vs REST - когда что использовать - Flashcards|Link to flashcards]]



---
### References:

