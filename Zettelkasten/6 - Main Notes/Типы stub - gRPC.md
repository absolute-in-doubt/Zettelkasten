
26-09-2026 13:12

Status:

Tags: [[gRPC]] [[Java+]]

---
# Типы stub - gRPC


**Stub** — сгенерированный клиентский класс, через который клиент вызывает удалённые методы gRPC-сервиса. Создаётся на основе `.proto`-контракта.

В Java обычно есть **3 основных типа stub**:

|Stub|Вызов|Когда использовать|
|---|---|---|
|**BlockingStub**|синхронный|Когда нужно дождаться ответа сразу|
|**FutureStub**|асинхронный, `ListenableFuture`|Когда нужен асинхронный результат|
|**Stub (AsyncStub)**|асинхронный, callback через `StreamObserver`|Для async-вызовов и streaming|

### 1. BlockingStub

```java
User user = blockingStub.getUser(request);
```

Поток **блокируется**, пока сервер не вернёт ответ.

Подходит для обычного request → response взаимодействия.


---
### 2. FutureStub

```java
ListenableFuture<User> future =
        futureStub.getUser(request);
```

Вызов не блокирует поток, результат приходит через `Future`.

Используется, когда нужен **асинхронный unary-вызов**.

> [!note]
> **Unary RPC** — самый простой тип gRPC-вызова: **один запрос → один ответ**.
> ```
> Client                    Server
  │                          │
  │──── Request ────────────>│
  │<──── Response ───────────│
> ```


---
### 3. AsyncStub

```java
asyncStub.getUser(request, new StreamObserver<>() {
    @Override
    public void onNext(User user) {}

    @Override
    public void onError(Throwable t) {}

    @Override
    public void onCompleted() {}
});
```

Результат обрабатывается через **`StreamObserver`**.

Особенно важен для **streaming**:

```text
Client → Server
Server → Client
Client ⇄ Server
```

### Главное для собеседования

> **BlockingStub** — синхронный вызов, 
> **FutureStub** — асинхронный через Future, 
> **AsyncStub** — асинхронный через `StreamObserver`; последний также используется для streaming.


----
#### [[Типы stub - gRPC - Flashcards|Link to flashcards]]



---
### References:

