
07-11-2025 07:42

Status: #baby 

Tags: [[Java Core]]

---
# Optional class - Java

==Not serializable==

Используется для возврата значения, обёрнутого в в Optional, либо пустого Optional.

Облегчает проверку возвращаемого значения.

----
### Создание

- ~={green}Optional.empty()=~ - создаёт пустой Optional

- ~={green}Optional.of(`T value`)=~ - создаёт гарантированно не пустой Optional

- ~={green}Optional.ofNullable(`T value`)=~ - не гарантирует, что созданный Optional не будет пустым. Если `value == null` -> возвратит пустой Optional.


### Проверки хранимого значения

- ~={green}.isPresent()=~ - если хранимое `value == null` -> returns false

- ~={green}.isEmpty()=~ - если хранимое `value == null` -> returns true


### Работа с wrapped values

(типа Stream API)

~={pink}Фактически все эти методы для одного Optional, НЕ ДЛЯ КОЛЛЕКЦИИ. Рофел крч.=~

- ~={green}.ifPresent(`Consumer<? super T> consumer`)=~ - consumer выполнится только, если `isPresent() == true`


- ~={green}.filter(`Predicate<? super T> predicate`)=~ - ~={red}фильтрует по предикату=~ lets the value stay if:
	- it's present
	- value matches predicate

- ~={green}.map(`Function<? super T, ? extends U> mapper`)=~ - ~={red}возвращает новое значение, полученное на основе=~ `value`, хранимой в Optional

- ~={green}.flatMap(`Function<? super T, Optional<U>> mapper`)=~ - то же самое, что и .map(), но ~={red}возвращает готовое значение=~, т.е. уже обёрнутое в Optional.

- ~={green}.or(`Supplier<Optional<? extends T>> supplier`)=~ - если Optional, к которому применяем, является пустым -> вызывается `supplier` -> он создаёт и ~={red}возвращает default Optional=~.

```java
Optional<String> value = Optional.of(expectedValue);

Optional<String> returnedValue = value.or(() -> Optional.of("default string"));
```

----
### Unwrapping

- ~={green}.orElse(`T defaultValue`)=~ 

- ~={green}.orElseGet(`Supplier<? extends T> supplier`)=~ - если Optional пустой, то вернётся значение, созданное `supplier`-ом

- ~={green}.orElseThrow(`Supplier<? extends Throwable> exceptionSupplier`)=~

-  ~={green}.get()=~ - если Optional пустой -> выкинет `NoSuchElementException`


----
#### [[Optional class - Java - Flashcards|Link to flashcards]]



---
### References:

