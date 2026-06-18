
Theory for the cards: 

FILE TAGS: algos

Q: Расскажи про prefixSum паттерн? Когда он применяется?
A:   
![[Pasted image 20260131165827.png]]
	
Префиксная сумма включает предварительную обработку массива для создания нового массива, где каждый элемент с индексом i представляет собой сумму массива от начала до i. Это позволяет эффективно выполнять запросы сумм к подмассивам.
	
**Пример задачи:**
	
Дан массив `nums`. Вычислите сумму элементов в диапазоне `[i, j]`.
	
Пример:
	
- Вход: `nums = [1, 2, 3, 4, 5, 6], i = 1, j = 3`
    
- Выход: 9
    
	
Объяснение:
	
- Создаём префиксный массив: `P = [1, 3, 6, 10, 15, 21]`
    
- Сумма элементов будет равна `[i, j] = P[j] - P[i-1] (при i > 0)`
	
---
	
**Когда использовать:** в ситуации, когда нужно выполнить несколько запросов сумм к подмассиву или вычислить совокупные суммы.
<!--ID: 1769869208507-->



Q: Каким паттерном можно решить следующую задачу:
	
Given an integer array `nums`, handle multiple queries of the following type:
	
1. Calculate the **sum** of the elements of `nums` between indices `left` and `right` **inclusive** where `left <= right`.
	
Implement the `NumArray` class:
	
- `NumArray(int[] nums)` Initializes the object with the integer array `nums`.
- `int sumRange(int left, int right)` Returns the **sum** of the elements of `nums` between indices `left` and `right` **inclusive** (i.e. `nums[left] + nums[left + 1] + ... + nums[right]`).
	
```java
class NumArray {
	
	public NumArray(int[] nums) {
	...
	}
	
	public int sumRange(int left, int right) {
	...
	}
}
```
A: Данная задача решается паттерном prefixSum. 
	
> **Почему?**
>
>Потому что присутствует несколько запросов к фиксированному массиву. Если не предобрабатывать массив, то каждый запрос будет обрабатываться за ~ O(n) (в худшем случае), а здесь - за O(1).
>
	
Решение:
	
```java
class NumArray {
	
	private int[] nums;
	
	public NumArray(int[] nums) {
		for(int i = 1; i < nums.length; i++){
			nums[i] = nums[i-1] + nums[i];
		}
		this.nums = nums;
	}
	
	public int sumRange(int left, int right) {
		int leftVal = (left == 0)? 0 : nums[left - 1];
		return nums[right] - leftVal;
	}
}
```
<!--ID: 1769869208530-->



Q: Как решать эту задачу? 
Given a binary array `nums`, return _the maximum length of a contiguous subarray with an equal number of_ `0` _and_ `1`.
```java
class Solution {
	
	public int findMaxLength(int[] nums) {
	}
}
```

A:  Данная задача решается паттерном prefixSum (немного адаптированным). 
	
Treat:
	
- `1` as `+1`
- `0` as `-1`
	
If two prefixes have the **same cumulative sum**,  
the subarray between them has equal numbers of 0s and 1s.
	
---
	
**Approach**
	
- Use a hashmap to store the **first index** where a cumulative sum appears.
- Initialize sum `0` at index `-1` to handle full-prefix cases. <- почему? - вопрос.
- Traverse the array:
    - Add `+1` for `1`, `-1` for `0`
    - If the sum was seen before, update maximum length
    - Otherwise, store the index
	
```java
class Solution {
	
	public int findMaxLength(int[] nums) {
		int count = 0;
		int max_length = 0;
		Map<Integer, Integer> indexes = new HashMap<>();
		indexes.put(0, -1);
		for(int i = 0; i<nums.length; i++){
			if(nums[i] == 0){
				count--;
			} else {
				count++;
			}
			
			if(indexes.containsKey(count)){
				max_length = Math.max(max_length, i - indexes.get(count));
			} else {
				indexes.put(count, i);
			}
		}
	return max_length;
	}
}
```
<!--ID: 1769932852448-->
