
29-01-2026 12:20

Status:

Tags: [[Java Core]]

---
# IO - Java Core

- `InputStream`, `OutputStream` - байтовые потоки
- `Reader, Writer` - символьные потоки (обёртки над `InputStream`, `OutputStream`)

```java
// БАЙТОВЫЕ потоки (InputStream, OutputStream)
InputStream (абстрактный)
├── FileInputStream
│   └── PushbackInputStream
├── ByteArrayInputStream  
├── ObjectInputStream
└── FilterInputStream
    ├── BufferedInputStream
    └── DataInputStream

OutputStream (абстрактный)
├── FileOutputStream
│   └── PrintStream
├── ByteArrayOutputStream
├── ObjectOutputStream
└── FilterOutputStream
    ├── BufferedOutputStream
    └── DataOutputStream

// СИМВОЛЬНЫЕ потоки (Reader, Writer)
Reader (абстрактный)           
├── CharArrayReader
├── InputStreamReader
│   └── FileReader
└── BufferedReader 
    └── LineNumberReader


Writer (абстрактный)
├── CharArrayWriter
├── OutputStreamWriter
│   └── FileWriter 
└── BufferedWriter
    └── PrintWriter 
```


#### InputStream

- `ByteArrayInputStream` позволяет использовать буфер в памяти (массив байтов) в качестве источника данных для входного потока;

- `FileInputStream` - входной поток для чтения информации из файла;

- `PushbackInputStream` - разновидность буферизации, обеспечивающая чтение байта с последующим его возвратом в поток, позволяет «заглянуть» во входной поток и увидеть, что оттуда поступит в следующий момент, не извлекая информации.

- `FilterInputStream` - абстрактный класс, предоставляющий интерфейс для классов-надстроек, которые добавляют к существующим потокам полезные свойства;
	
	- `DataInputStream` - входной поток для байтовых данных, включающий методы для чтения стандартных типов данных Java;
	
	- `BufferedInputStream` - буферизованный входной поток;

- `ObjectInputStream` - входной поток для объектов;


#### OutputStream

- `OutputStream` - это абстрактный класс, определяющий потоковый байтовый вывод;

- `FileOutputStream` - запись данных в файл на физическом носителе;

- `PrintStream` - выходной поток, включающий методы `print()` и `println()`.

- `ByteArrayOutputStream` - все данные, посылаемые в этот поток, размещаются в предварительно созданном буфере;

- `FilterOutputStream` - абстрактный класс, предоставляющий интерфейс для классов-надстроек, которые добавляют к существующим потокам полезные свойства;
	
	- `BufferedOutputStream` - буферизированный выходной поток;
	
	- `DataOutputStream` - выходной поток байт, включающий методы для записи стандартных типов данных Java;

- `ObjectOutputStream` - выходной поток для записи объектов;

- `PipedOutputStream` реализует понятие выходного канала;




----
#### [[IO - Java Core - Flashcards|Link to flashcards]]



---
### References:

