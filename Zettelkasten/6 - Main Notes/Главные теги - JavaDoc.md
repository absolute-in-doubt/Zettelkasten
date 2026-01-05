
28-12-2025 11:40

Status: #adult 

Tags: [[Документирование]] [[Java+]]

---
# Главные теги - JavaDoc

| Тег                  | Где используется             | Краткий смысл                                                 |
| -------------------- | ---------------------------- | ------------------------------------------------------------- |
| @param               | У методов/конструкторов      | Описывает параметр: его имя, назначение, допустимые значения. |
| @return              | У методов                    | Описывает, что именно возвращает метод и в каких случаях.     |
| @throws / @exception | У методов                    | Какие исключения может выбросить метод и при каких условиях.  |
| @author              | У классов/интерфейсов        | Автор/ы класса или модуля.                                    |
| @version             | У классов/интерфейсов        | Версия API или компонента.                                    |
| @since               | Везде                        | С какой версии библиотеки/модуля появился элемент.            |
| @deprecated          | Везде                        | Элемент устарел; обычно с пояснением и альтернативой.         |
| @see                 | Везде                        | Ссылка на связанный класс/метод/документацию.                 |
| @link                | Внутри описания              | Встроенная ссылка на другой элемент API.                      |
| @code                | Внутри описания              | Выделение фрагмента как кода (инлайн).                        |
| @literal             | Внутри описания              | Вывод текста «как есть», без обработки HTML.                  |
| @serial              | У полей/классов              | Описание сериализуемых полей.                                 |


### Мой пример


1. Сначала желательно добавлять описание без тега

2. Если нужно определенноу форматирование - используем HTML теги

```java
/**
 * Handles parsing of XML elements stored in _calc.xml files.
 *
 * This method parses calculation links and builds
 *           an internal representation of calculation arcs.
 *
 * Parses the following elements and attributes:
 * <ul>
 *   <li><code>calculationArc</code>:
 *     <ul>
 *       <li><code>xlink:from</code> – owner identifier (close to GAAP name, but some companies append an id)</li>
 *       <li><code>xlink:to</code> – child identifier</li>
 *       <li><code>weight</code> – coefficient multiplied by the child element value in the formula</li>
 *     </ul>
 *   </li>
 * </ul>
 */

```


----
#### [[Главные теги - JavaDoc - Flashcards|Link to flashcards]]



---
### References:

