
03-11-2025 10:06

Status:

Tags:

---
# HashMap implementation - Java Core

Содержит массив, где каждый элемент - bucket.

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

LinkedList превращается в BinaryTree:

![[Pasted image 20251103102710.png]]



----
#### [[HashMap implementation - Java Core - Flashcards|Link to flashcards]]



---
### References:

