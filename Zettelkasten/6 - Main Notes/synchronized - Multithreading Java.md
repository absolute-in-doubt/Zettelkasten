
21-01-2026 20:18

Status:

Tags: [[Multithreading]] [[Java+]]

---
# synchronized - Multithreading Java


При входе в `synchronized` метод или блок поток обновляет содержимое локальной памяти, а при выходе из `synchronized` метода или блока поток записывает изменения, сделанные в локальной памяти, в главную.

> [!tip] Note
> При использовании synchronized на методе объекта - синхронизация идет по самому объекту:
> ```java
> class MyClass {
>	public synchronized void foo(){...}
>	
>	public static void main(String[] args){
>		MyClass obj = new MyClass();
>		obj.foo(); //синхронизация по объекту obj
>	}
>}
>
> ```

~={orange}**Если бы метод был статическим, то вместо ссылки на объект мы бы передали имя класса, и класс стал бы монитором для синхронизации блока.**=~

> [!warning]
> выполнение synchronized static метода не блокирует создание новых объектов этого или других классов.



----
#### [[synchronized - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

