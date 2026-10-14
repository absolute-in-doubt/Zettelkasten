
19-09-2026 11:21

Status:

Tags: [[jcmd - JVM Diagnostics & Monitoring]] [[JVM Diagnostics & Monitoring]]

---
# VM commands - jcmd


#### `VM.command_line`


```bash
PS C:\java-workspace\hotels-rest\hotels-rest> jcmd 35852 VM.command_line
35852:
VM Arguments:
jvm_args: -XX:TieredStopAtLevel=1
java_command: com.example.hotels.App
java_class_path (initial): C:\java-workspace\hotels-rest\hotels-rest\target\classes;C:\Users\???\.m2\repository\org\springframework\boot\spring-boot\3.5.7\spring-boot-3.5.7.jar;
... # Все классы и jars, что есть в classpath
C:\Users\???\.m2\repository\com\h2database\h2\2.2.224\h2-2.2.224.jar
Launcher Type: SUN_STANDAR
```

Выведет команду со всеми аргументами, которая использовалась для запуска приложения.


---
#### `VM.flags`

```bash
PS C:\java-workspace\hotels-rest\hotels-rest> jcmd 35852 VM.flags
35852:
-XX:CICompilerCount=12 -XX:ConcGCThreads=3 -XX:G1ConcRefinementThreads=13 -XX:G1EagerReclaimRemSetThreshold=16 -XX:G1HeapRegionSize=2097152 -XX:G1RemSetArrayOfCardsEntries=16 -XX:G1RemSetHowlMaxNumBuckets=8 -XX:G1RemSetHowlNumB
uckets=8 -XX:InitialHeapSize=255852544 -XX:MarkStackSize=4194304 -XX:MarkStackSizeMax=536870912 -XX:MaxHeapSize=4078960640 -XX:MaxNewSize=2447376384 -XX:MinHeapDeltaBytes=2097152 -XX:MinHeapSize=8388608 -XX:NonNMethodCodeHeapSize=4096 -XX:NonProfiledCodeHeapSize=0 -XX:-ProfileInterpreter -XX:ProfiledCodeHeapSize=0 -XX:SoftMaxHeapSize=4078960640 -XX:TieredStopAtLevel=1 -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation
```

Выведет все флаги JVM, которые использовались для запуска JVM под этот процесс.
Большая часть из них применяется автоматически, если явно не заданы в команде запуска.


---
#### `VM.version`

```bash
PS C:\java-workspace\hotels-rest\hotels-rest> jcmd 35852 VM.version
35852:
Java HotSpot(TM) 64-Bit Server VM version 23.0.1+11-39
JDK 23.0.1
```


----
#### [[VM commands - jcmd - Flashcards|Link to flashcards]]



---
### References:

