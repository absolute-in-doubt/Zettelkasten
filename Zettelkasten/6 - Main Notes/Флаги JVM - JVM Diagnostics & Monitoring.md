
24-09-2026 19:33

Status:

Tags: [[JVM Diagnostics & Monitoring]] [[Java+]]

---
# Флаги JVM - JVM Diagnostics & Monitoring


### Default flags

- `-XX:CICompilerCount` — количество потоков JIT-компилятора, которые JVM выделяет для компиляции Java-кода в машинный код.

- `-XX:ConcGCThreads` — количество потоков, используемых для фоновых concurrent-фаз GC.

- `-XX:G1ConcRefinementThreads` — количество потоков для фоновой обработки карточек памяти (Card Refinement) в G1.

- `-XX:G1EagerReclaimRemSetThreshold` — порог размера remembered set, после которого G1 может пытаться досрочно освобождать объекты/регионы.

- `-XX:G1HeapRegionSize` — размер одного региона (Region) G1 Heap; `2097152` = **2 MB**.

- `-XX:G1RemSetArrayOfCardsEntries` — размер массива записей карточек, используемого G1 для remembered sets.

- `-XX:G1RemSetHowlMaxNumBuckets` — максимальное количество bucket'ов в структуре хранения remembered-set entries.

- `-XX:G1RemSetHowlNumBuckets` — количество bucket'ов, используемых этой структурой remembered set.

- `-XX:InitialHeapSize` — начальный размер Java Heap; `255852544` ≈ **244 MB**.

- `-XX:MarkStackSize` — начальный размер стека, используемого G1 для маркировки объектов; `4194304` = **4 MB**.

- `-XX:MarkStackSizeMax` — максимальный размер этого стека; `536870912` = **512 MB**.

- `-XX:MaxHeapSize` — максимальный размер Java Heap; `4078960640` ≈ **3.8 GB**.

- `-XX:MaxNewSize` — максимальный размер Young Generation; `2447376384` ≈ **2.28 GB**.

- `-XX:MinHeapDeltaBytes` — минимальный размер, на который JVM изменяет размер Heap при его динамической адаптации; `2097152` = **2 MB**.

- `-XX:MinHeapSize` — минимальный размер Java Heap; `8388608` = **8 MB**.

- `-XX:NonNMethodCodeHeapSize` — размер Code Heap для native stubs и другого кода, который не является скомпилированными Java-методами; ≈ **7.25 MB**.

- `-XX:NonProfiledCodeHeapSize` — размер Code Heap для скомпилированного кода, который не требует profiling; ≈ **116 MB**.

- `-XX:ProfiledCodeHeapSize` — размер Code Heap для профилируемого JIT-кода; ≈ **116 MB**.

- `-XX:ReservedCodeCacheSize` — общий максимальный размер Code Cache, где JVM хранит JIT-сгенерированный машинный код; `251658240` ≈ **240 MB**.

- `-XX:+SegmentedCodeCache` — разделяет Code Cache на несколько специализированных Code Heap.

- `-XX:SoftMaxHeapSize` — мягкий целевой максимум Heap, ниже которого GC старается удерживать размер Heap; при необходимости Heap может вырасти до `MaxHeapSize`.

- `-XX:+UseCompressedOops` — использует сжатые 32-битные ссылки на объекты вместо полноразмерных 64-битных, уменьшая расход памяти.

- `-XX:+UseG1GC` — включает G1 Garbage Collector.

- `-XX:-UseLargePagesIndividualAllocation` — отключает индивидуальное выделение больших memory pages для Java Heap.


### Additional flags

- `-Xms64m` — **начальный размер Java Heap = 64 MB**. По сути устанавливает значение `-XX:InitialHeapSize=64m`. Если не указать, JVM сама выбирает начальный размер на основе доступной памяти/характеристик системы.
    
- `-Xmx128m` — **максимальный размер Java Heap = 128 MB**. Устанавливает `-XX:MaxHeapSize=128m`. Если не указать, JVM сама рассчитывает максимальный размер Heap (на современных JVM — с учётом доступной физической/контейнерной памяти).
    
- `-XX:+HeapDumpOnOutOfMemoryError` — автоматически создаёт **heap dump (`.hprof`) при `OutOfMemoryError`**. По умолчанию выключен (`false`).
    
- `-XX:HeapDumpPath=./heapdump.hprof` — указывает **куда сохранить heap dump**. Сам по себе этот параметр не включает создание dump — для этого нужен `-XX:+HeapDumpOnOutOfMemoryError`. Если путь не указать, JVM использует имя/расположение по умолчанию для heap dump.
    

##### Связь `-Xms` / `-Xmx` с `-XX`

Можно запомнить:

```
-Xms64m  →  -XX:InitialHeapSize=67108864
-Xmx128m →  -XX:MaxHeapSize=134217728
```

То есть:

```
-Xms → InitialHeapSize
-Xmx → MaxHeapSize
```

А вот, например, `-XX:MinHeapSize` и `-XX:MaxNewSize` **не являются прямыми аналогами** `-Xms`/`-Xmx`.

----
#### [[Влаги JVM - JVM Diagnostics & Monitoring - Flashcards|Link to flashcards]]



---
### References:

