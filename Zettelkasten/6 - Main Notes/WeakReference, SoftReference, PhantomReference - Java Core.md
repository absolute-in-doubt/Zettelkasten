
18-01-2026 09:47

Status:

Tags: [[Java Core]]

---
# WeakReference, SoftReference, PhantomReference - Java Core

По силе :
1. `SoftReference` - самая сильная
2. `WeakReference`
3. `PhantomReference` - самая слабая


---
### SoftReference

`SoftReference` — это самая сильная из всех перечисленных ссылок. Если на объект не осталось больше нормальных ссылок, а только `SoftReference`, объект не будет съеден сборщиком мусора до тех пор, пока реально не возникнет ситуация нехватки памяти.

Хороший пример использования для таких ссылок — кеш больших картинок в памяти. Если память исчерпалась, картинку выбросит сборщик мусора, и вы сможете перечитать её с диска, когда она снова вам понадобится.

```java
StringBuilder builder = new StringBuilder(); //strong ссылка
SoftReference<StringBuilder> softBuilder = new SoftReference(builder);
builder = null;

softBuilder.get() // вернет strong-ссылку на объект StringBuilder в случае если GC не удалил этот объект из памяти. В другом случае вернется null. 
 
softBuilder.clear() // удалит ссылку на объект StringBuilder (то есть soft-ссылки на этот объект больше нет)
```


---
### WeakReference

`WeakReference` слабее: она не увеличивает дополнительно время жизни объекта, на который ссылается, и если на объект есть не более чем слабые ссылки, сборщик мусора может убрать его когда ему вздумается.

Хороший пример использования для таких ссылок — добавить дополнительную информацию об каком-то объекте. Для этого вы держите в своём контейнере не сам объект, а лишь `WeakReference` на него, вместе с необходимой информацией, тем самым вы не мешаете объекту умереть вовремя и не меняете свойства остальной части программы.

> [!tip] 
> Имея на руках `SoftReference` или `WeakReference` на ещё живой объект, вы можете получить настоящую ("сильную") ссылку, и предотвратить съедение этого объекта сборщиком мусора. Имея сильную ссылку, вы можете работать с объектом как обычно.


---
### PhantomReference

`PhantomReference` ещё слабее. Она не только не предохраняет объект от уборки, она даже не даёт возможности получить сильную ссылку. Вы можете только узнать, что объект собирается умереть, и предпринять какие-то действия по очистке; предотвратить смерть объекта вы не сможете.


---
## ReferenceQueue

Позволяет отслеживать момент, когда GC определит что объект более не нужен и его можно удалить. Именно ~={red}сюда попадает ***Reference объект***=~ после того как объект на который он ссылается удален из памяти. 

При создании Reference мы можем передать в конструктор ReferenceQueue, в который будут помещаться ссылки после удаления.

```java
public class SoftImageCache {

    // Очередь для уведомления о сборке мягко достижимых объектов
    private final ReferenceQueue<BufferedImage> queue = new ReferenceQueue<>();

    // Ключ -> SoftReference на картинку
    private final Map<String, SoftReference<BufferedImage>> cache = new HashMap<>();

    /**
     * Основной метод получения изображения.
     * Возвращает картинку из кэша, либо загружает с диска и кладёт в кэш.
     */
    public synchronized BufferedImage get(String path) throws IOException {
        // Сначала чистим кэш от «мертвых» ссылок
        processQueue();

        SoftReference<BufferedImage> ref = cache.get(path);
        BufferedImage img = (ref != null) ? ref.get() : null;

        if (img != null) {
            // Объект ещё в памяти
            return img;
        }

        // Объект уже был выкинут GC или не кэшировался — загружаем заново
        img = loadImage(path);

        // Кладём новую SoftReference, привязанную к очереди
        cache.put(path, new SoftReference<>(img, queue));
        return img;
    }

    /**
     * Обработка очереди: удаляем из map те записи, чьи SoftReference были очищены GC.
     */
    private void processQueue() {
        Reference<? extends BufferedImage> ref;
        while ((ref = queue.poll()) != null) {
            // ref — SoftReference, которую GC очистил и поместил в queue
            // Нужно найти и удалить её из cache
            Iterator<Map.Entry<String, SoftReference<BufferedImage>>> it = cache.entrySet().iterator();
            while (it.hasNext()) {
                Map.Entry<String, SoftReference<BufferedImage>> e = it.next();
                if (e.getValue() == ref) {
                    it.remove();
                    break;
                }
            }
        }
    }

    private BufferedImage loadImage(String path) throws IOException {
        // Реальный код может быть чтением из диска, сети, БД и т.п.
        try (var in = Files.newInputStream(Path.of(path))) {
            return ImageIO.read(in);
        }
    }
}

```


> [!warning]
> При создании `SoftReference`, `WeakReference` вы можете, а при создании `PhantomReference` должны указать `ReferenceQueue` (хотя тут можно указать `null`, это обычно бессмысленно). После того, как объект будет убран сборщиком мусора, ссылка попадает в указанную вами `ReferenceQueue`.


---

Для нефантомных ссылок при добавлении в очередь [[Object class - Java Core#Method `finalize()`|финализатор]] уже выполнен и память объекта уже освобождена, но для фантомных добавление происходит после вызова финализатора до очистки памяти. Вы можете по сути не объявлять дорогой финализатор, а воспользоваться фантомной ссылкой из очереди для того, чтобы самостоятельно освободить ассоциированные ресурсы. (Для этого вы не сможете использовать сам объект, т. к. сильную ссылку на него невозможно получить из фантомной ссылки; но вы можете унаследоваться от `PhantomReference`, чтобы добавить нужную информацию в объект-ссылку.)

Ещё одно отличие состоит в том, что фантомную ссылку вы должны очистить вручную, иначе объект будет оставаться (фантомно) достижим. Только после этого объект будет окончательно удалён.


----
#### [[WeakReference, SoftReference, PhantomReference - Java Core - Flashcards|Link to flashcards]]



---
### References:

