
06-11-2025 10:25

Status: #baby 

Tags: [[Testing]] [[Java+]]

---
# Параметризованные тесты - JUnit

Реализуется пакетом `junit-jupiter-params`:

```xml
<dependency>  
  <groupId>org.junit.jupiter</groupId>  
  <artifactId>junit-jupiter-params</artifactId>  
  <version>5.11.3</version>  
  <scope>test</scope>  
</dependency>
```

Над ними ставим ~={purple}@ParameterizedTest=~. И вдобавок к этому аннотацию источника данных.

----
#### Repeatable annotations:

- _@ValueSource_
- _@EnumSource_
- _@MethodSource_
- _@FieldSource_
- _@CsvSource_
- _@CsvFileSource_
- _@ArgumentsSource_
- _@NullAndEmptySource_
- _@NullSource_
- _@EmptySource_


---
### Аннотации источников данных

#### @ValueSource()

В ней можем передать массив аргументов определённого типа.
Доступные типы:
- _short_ (with the _shorts_ attribute)
- _byte_ (_bytes_ attribute)
- _int_ (_ints_ attribute)
- _long_ (_longs_ attribute)
- _float_ (_floats_ attribute)
- _double_ (_doubles_ attribute)
- _char_ (_chars_ attribute)
- _java.lang.String_ (_strings_ attribute)
- _java.lang.Class_ (_classes_ attribute)

Пример использования:

```java
@ParameterizedTest
@ValueSource(strings = {"", "  "})
void isBlank_ShouldReturnTrueForNullOrBlankStrings(String input) {
    assertTrue(Strings.isBlank(input));
}

@ParameterizedTest
@ValueSource(ints = {1, 3, 5, -3, 15, Integer.MAX_VALUE}) // six numbers
void isOdd_ShouldReturnTrueForOddNumbers(int number) {
    assertTrue(Numbers.isOdd(number));
}
```


#### @NullSource
Передаёт единственный параметтр null.

```java
@ParameterizedTest
@NullSource
void isBlank_ShouldReturnTrueForNullInputs(String input) {
    assertTrue(Strings.isBlank(input));
}
```

#### @EmptySource

Передаёт один пустой аргумент в зависимости от типа параметра. (Если строка, передаёт пустую строку, если коллекция - пустую коллекцию)

```java
@ParameterizedTest
@EmptySource
void isBlank_ShouldReturnTrueForEmptyStrings(String input) {
    assertTrue(Strings.isBlank(input));
}
```

#### @NullAndEmptySource

Передаёт два параметра: null и пустой параметр (аналогично @EmptySource).

```java
@ParameterizedTest
@NullAndEmptySource
void isBlank_ShouldReturnTrueForNullAndEmptyStrings(String input) {
    assertTrue(Strings.isBlank(input));
}
```

> [!note] 
> Можно комбинировать несколько аннотаций. Например @NullAndEmptySource и @ValueSource:
> ```java
> @ParameterizedTest
>@NullAndEmptySource
>@ValueSource(strings = {"  ", "\t", "\n"})
>void isBlank_ShouldReturnTrueForAllTypesOfBlankStrings(String input) {
>    assertTrue(Strings.isBlank(input));
>}
> ```


#### @EnumSource

Передаёт данные из определённого Enum:

```java
@ParameterizedTest
@EnumSource(Month.class) // passing all 12 months
void getValueForAMonth_IsAlwaysBetweenOneAndTwelve(Month month) {
    int monthNumber = month.getValue();
    assertTrue(monthNumber >= 1 && monthNumber <= 12);
}
```

```java
@ParameterizedTest
@EnumSource(value = Month.class, names = {"APRIL", "JUNE", "SEPTEMBER", "NOVEMBER"})
void someMonths_Are30DaysLong(Month month) {
    final boolean isALeapYear = false;
    assertEquals(30, month.length(isALeapYear));
}
```

Более тонкая настройка [тут](https://www.baeldung.com/parameterized-tests-junit-5#3-enum)


#### @FieldSource

Позволяет взять данные поля, чтобы не писать каждый раз одни и те же данные.

```java
static List<String> cities = Arrays.asList("Madrid", "Rome", "Paris", "London");

@ParameterizedTest
@FieldSource("cities")
void isBlank_ShouldReturnFalseWhenTheArgHasAtLEastOneCharacter(String arg) {
    assertFalse(Strings.isBlank(arg));
}
```


#### @MethodSource

Передача аргументов, генерируемых методом. Метод может возвращать как **stream**, так и любую другую коллекцию.

```java
private static Stream<Arguments> provideStringsForIsBlank() {
    return Stream.of(
      Arguments.of(null, true),
      Arguments.of("", true),
      Arguments.of("  ", true),
      Arguments.of("not blank", false)
    );
}

@ParameterizedTest
@MethodSource("provideStringsForIsBlank")
void isBlank_ShouldReturnTrueForNullOrBlankStrings(String input, boolean expected) {
    assertEquals(expected, Strings.isBlank(input));
}
```


---
### Что еще можно использовать для передачи аргументов в тест:

- данные а-ля CSV (testValue, expectedValue) [тык](https://www.baeldung.com/parameterized-tests-junit-5#4-csv-literals)
- CSV файлы [тык](https://www.baeldung.com/parameterized-tests-junit-5#5-csv-files)
- [Custom Argument Provider](https://www.baeldung.com/parameterized-tests-junit-5#8-custom-argument-provider)
-  и ещё куча приколов [тут](https://www.baeldung.com/parameterized-tests-junit-5#4-csv-literals)


----
#### [[Параметризованные тесты - JUnit - Flashcards|Link to flashcards]]



---
### References:

