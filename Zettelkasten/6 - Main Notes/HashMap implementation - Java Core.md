
03-11-2025 10:06

Status:

Tags: [[Java Core]]

---
# HashMap implementation - Java Core

Содержит массив, где каждый элемент - bucket.

Default amount of buckets = 16. Используя конструкторы с параметрами можно задавать произвольное начальное количество корзин.

~={orange}**HashMap в Java автоматически увеличивает количество bucket-ов при достижении порогового значения загрузки (threshold).**=~ Это происходит **и** при превышении 75% заполнения таблицы (load factor = 0.75 по умолчанию), **и** независимо от преобразования содержимого bucket-ов в деревья.

|Событие|Условие|Действие|
|---|---|---|
|**Resize**|`size > capacity × 0.75`|Увеличение bucket-ов ×2|
|**Treeify**|Bucket > 8 элементов|LinkedList → Tree|
|**Untreeify**|Bucket < 6 элементов|Tree → LinkedList|

> [!note]
> - **size**: общее количество пар ключ-значение (13 в вашем примере). Метод `hashMap.size()` вернёт 13.
>    
>- **capacity**: размер внутреннего массива бакетов (16 по умолчанию)

**~={red}?!=~** размер массива бакетов обычно вырастет **в 2 раза: с 16 до 32**.

**Скорость чтения:**
- O(log n) - т.к. в худшем случае читаем из RB tree
**Скорость записи:**
- O(log n) + resize - т.к. в худшем случае записываем в RB tree

---
### Simple situation:

Для каждого вставляемого элемента (пары key-value). создаётся объект `Node<K,V>`:
```java
class Node<K, V> implements Map.Entry<K,V> {
	int hash;
	K key;
	V value;
	Node<K, V> next;
	
	...
}
```

~={green}Calling hashCode() for key=~
этот hashCode делится на количество элементов в массиве и помещается в bucket, номер которого равен номеру остатка от деления:

```java
bucketNum = hash % arraylength
```


![[Pasted image 20251103101631.png]]

### Если в bucket с этим номером уже есть элемент

~={green}Calling equals() for the new key and each stored key=~ - чтобы удостовериться, что такого ключа еще нет.

Тогда новый элемент ставится после уже существующего. В предыдущий Node сохраняется ссылка на новый (в поле `Node<K, V> next`). Создаётся [[LinkedList implementation - Java Core|singly LinkedList]]:

![[Pasted image 20251103102323.png]]

#### Поиск по HashMap с LinkedLists в buckets:

```pseudocode
bucketNum = key.hashcode() -> получили номер bucket
storedKey = buckets[bucketNum]

В bucket сравниваем с каждым ключом по порядку:
while(storedKeyValue.next != null){
	if(key.equals(storedKeyValue))
		return storedKeyValue
		
	storedKeyValue = storedKeyVAlue.next
}
```


### Если в одном bucket становится слишком много элементов (~={cyan}больше 8=~)

LinkedList превращается в BinaryTree (**~={red}red-black Binary Tree=~**):

![[Pasted image 20251103102710.png]]


BinaryTree обычно строится с помощью метода `compareTo(Object obj)` интерфейса `Comparable`. Но `HashMap` не требует, чтобы ключи реализовывали `Comparable`. Поэтому алгоритм балансировки дерева такой:

1. Сначала делается попытка сравнить хэши ключей

2. Если хэши равны и оба ключа реализуют `Comparable`, то для сравнения вызывается метод `compareTo()`

3. Если ключи не реализуют `Comparable`, то сравнение происходит с помощью метода `tieBreakOrder()`, в котором
	
    - сначала будет совершена попытка сравнить ключи через названия их классов (`getClass().getName()`)
	
    - если ключи являются экземплярами одного класса, то сравниваться будут результаты метода `System.identityHashCode()`



----
#### [[HashMap implementation - Java Core - Flashcards|Link to flashcards]]



---
### References:

