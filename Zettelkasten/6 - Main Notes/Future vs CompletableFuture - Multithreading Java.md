
07-05-2026 19:56

Status:

Tags: [[Multithreading]] [[Java+]]

---
# Future vs CompletableFuture - Multithreading Java





| Аспект                     | Future                                                        | CompletableFuture                                                                                   |
| -------------------------- | ------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| **API**                    | Базовые методы: `get()` (блокирующий), `isDone()`, `cancel()` | Расширяет `Future` + `CompletionStage`: `thenApply()`, `thenCompose()`, `thenCombine()` для цепочек |
| **Композиция**             | Нет поддержки цепочек или комбинаций задач                    | Поддержка: `thenApplyAsync()`, `allOf()`, `anyOf()` для параллельного выполнения                    |
| **Обработка ошибок**       | Только через `get()` (ExecutionException)                     | `exceptionally()`, `handle()` для неблокирующей обработки                                           |
| **Завершение**             | Только ExecutorService завершает                              | Можно завершить вручную: `complete()`, `completeExceptionally()`                                    |
| **Неблокирующие операции** | Нет, `get()` блокирует                                        | `thenAccept()`, `thenRun()` — колбэки без блокировки                                                |

## Где используется CompletableFuture

Хотя `ExecutorService.submit()` возвращает `Future`, CompletableFuture применяют для **реактивного стиля** и **композиции асинхронных операций**.

- **Создание**: `CompletableFuture.supplyAsync(() -> heavyTask())` — напрямую, без ExecutorService.
    
- **В микросервисах**: цепочки вызовов API (fetchUser → fetchOrders → calculateTotal) без блокировки основного потока.
    
- **Параллельные задачи**: `allOf(f1, f2).thenRun(combineResults)` вместо нескольких `future.get()`.
    
- **Веб-сервисы**: неблокирующие HTTP-клиенты (WebClient в Spring возвращает Mono, аналог CF).
    
- **Пример миграции**: Замените `Future<String> f = executor.submit(task); String res = f.get();` на `CompletableFuture.supplyAsync(task).thenApply(String::toUpperCase).join();`.
    

CompletableFuture идеален для высоконагруженных систем, где Future слишком примитивен.



----
#### [[Future vs CompletableFuture - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

