
21-01-2026 16:33

Status:

Tags: [[Multithreading]] [[Java+]]

---
# wait(), notify(), notifyAll() - Multithreading Java

- `wait()` — поток ждёт и **~={red}отпускает монитор=~**.
    
- `notify()` — будит один поток, ждущий **~={orange}на этом объекте=~**.
    
- `notifyAll()` — будит всех.

> [!warning]
> `wait()` и `notify()` методы вызываются только в **synchronized** блоке

-`notify()` будит поток(и), ожидающие на данном мониторе. Чтобы корректно выбрать монитор и не допустить гонок (lost wakeup и т.п.), поток обязан быть «хозяином» монитора при вызове. Если вызвать `notify()` без владения монитором, спецификация требует бросить `IllegalMonitorStateException`.

----
#### [[wait(), notify(), notifyAll() - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

